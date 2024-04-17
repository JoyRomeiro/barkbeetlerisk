library(data.table)
library(magrittr)
library(gridExtra)
library(tidyr)
library(dplyr)
library(ggplot2)
library(readxl)
library(pbapply)
library(parallel)
library(viridis)
library(ggh4x)

### SEIDL'S MODELS AND OTHER FUNCTIONS

seidl_prob_coef = c(4.234,-1.196,-0.0433,0.658,-1.425,-2.606,-0.214,-0.000383,-0.0527,0.0993,0.245,0.00746,-0.104,0.278,0.457,-0.301)

s_proc = function(n, h_dom) {
  return(100 * sqrt(10000. / n) / h_dom)
}

predict_barkbeetle_probability = function(temp, prec, age, n, hDom, n_spruce) {
  t = log(temp)
  p = log(prec)
  rd = 10 / s_proc(n, hDom)
  hs = n_spruce / n
  e50 = ifelse(hs > 0.25 & hs <= 0.65, 1, 0)
  e100 = ifelse(hs > .65, 1, 0)
  predictors = c(
    t,
    p,
    age,
    rd,
    e50,
    e100,
    t * p,
    t * age,
    t * rd,
    t * e50,
    t * e100,
    p * age,
    p * rd,
    p * e50,
    p * e100,
    1
  )
  
  z =  sum(predictors*seidl_prob_coef)
  result = exp(z) / (1 + exp(z))
  return(result)
}

smi_coef = c(
  0.00107, # temp
  25490, # prec^-2
  8282, # temp x prec^-2
  -0.0413 # intercept
)

predict_SMI = function(temp, prec) {
  result = sum(smi_coef*c(temp,prec^-2,temp * prec^-2,1))
  return (result)
}

predict_barkbeetle_intensity = function(temp, prec, n, n_spruce) {
  SEE = 1
  SMI = predict_SMI(temp, prec)
  HTS = n_spruce / n
  SHI = SEE * SMI * HTS
  result = 1 / (1 + exp(3.9725 - 2.9673 * SHI))
  return (result)
}

predict_barkbeetle_intensity = Vectorize(predict_barkbeetle_intensity,c("temp", "prec", "n", "n_spruce"))
predict_barkbeetle_probability = Vectorize(predict_barkbeetle_probability,c("temp", "prec", "age", "n", "hDom", "n_spruce"))

get_bit = function(number, i) {
  binary_str = intToBits(number)
  ith_bit = binary_str[i]
  as.numeric(ith_bit)
}

predict_probability_comb = function(temp, prec, age, n, hDom, n_spruce, years, comb){
  bits = as.numeric(intToBits(comb)[1:years])
  probs = sapply(1:years, function(i) predict_barkbeetle_probability(temp, prec, age + i, n, hDom, n_spruce))
  product = prod(bits * probs + (1 - bits) * (1 - probs))
  return(product)
}

predict_intensity_comb = function(temp, prec, n, n_spruce, years, comb){
  bits = as.numeric(intToBits(comb)[1:years])
  intensities = sapply(1:years, function(i) (1 - get_bit(comb, i) * predict_barkbeetle_intensity(temp, prec, n, n_spruce)))
  product = 1 - prod(intensities)
  return(product)
}

predict_expected_intensity_period = function(temp, prec, age, n, hDom, n_spruce, d, years){
  combs = 0:(2^years - 1)
  expected_intensity = sum(sapply(combs, function(k) {
    predict_probability_comb(temp, prec, age, n, hDom, n_spruce, years, k) *
      predict_intensity_comb(temp, prec, n, n_spruce, years, k)
  }))
  if (d<15)  expected_intensity = 0
  return(expected_intensity)
} 

predict_expected_intensity_period = Vectorize(predict_expected_intensity_period,c("temp", "prec", "age", "n", "hDom", "n_spruce", "d", "years"))

### RAW INPUT 

path.project = "."

stand_data = read_excel(paste0(path.project, "/Standsforsimulation_new_2702.xlsx"), sheet = "Selected") %>%
  as.data.table() %>%
  .[, .(stand = `Stand no`, si = `Site index spruce`, age_start = `TT (yr)`, density = Density, host = `Host tree (%)`)]

mai_data = data.table(si = c(23,20,17,14,11), MAI = c(70, 80, 90, 100, 110))
stand_data = merge(stand_data, mai_data, by = "si", all.x = TRUE)
stand_data[, rel_age := age_start/MAI]
stand_data[, age_class := ifelse(rel_age < 0.6, "young", "old")]


period_data = fread(paste0(path.project,"/Data_risk_27022023_schedules.csv")) %>%
  .[, .(stand, schedule, period, age = `age (yr)`, n = `n (/ha)`, n_spruce = `n-G (/ha)`, d = `d (cm)`, hDom = `hDom (m)`, harvest = Treatment/7, npv =`NPV total`)]


### BARK BEETLE CALCULATIONS

harvest_periods = period_data[harvest == 1, .(harvest_period = min(period)), by = .(stand, schedule)] %>%
  rbind(data.table(stand=1:300,schedule=1,harvest_period=13))

npv_period_12 = period_data[period == 12, .(stand, schedule, npv)]
period_data = period_data[npv_period_12, on = .(stand, schedule), npv := i.npv]
period_data = period_data[harvest_periods, on = .(stand, schedule), nomatch = 0]

age_info = stand_data[, .(second_max_age = sort(unique(age_start), decreasing = TRUE)[2]), by = si]

keep_stands = stand_data[age_info, on = "si"][(age_start < second_max_age)&(host>20)]$stand

period_data = period_data[stand %in% keep_stands]

period_data_x = period_data[harvest_period==1]

period_data = period_data[period < harvest_period]

temperatures = c(0.1,1:15)
precipitations = seq(500,2000,100)

temp_prec_combinations = expand.grid(temp = temperatures, prec = precipitations)
temp_prec_period_data =  cross_join(period_data, temp_prec_combinations)
temp_prec_period_data_x =  cross_join(period_data_x, temp_prec_combinations)
temp_prec_period_data_x$exp_intensity = 0

no_of_chunks = 96
chunks = split(temp_prec_period_data, cut(seq(nrow(temp_prec_period_data)), no_of_chunks, labels = FALSE))
cl = makeCluster(no_of_chunks)

clusterEvalQ(cl, library(data.table))

clusterExport(cl, c("predict_expected_intensity_period",
                    "predict_probability_comb", 
                    "predict_intensity_comb",
                    "get_bit",
                    "predict_barkbeetle_intensity",
                    "predict_barkbeetle_probability",
                    "predict_SMI",
                    "smi_coef",
                    "seidl_prob_coef",
                    "s_proc"),
              envir = environment())

system.time({
  results = parLapply(cl, chunks, function(chunk) {
    chunk[, exp_intensity := predict_expected_intensity_period(temp, prec, age, n, hDom, n_spruce, d, 5)]
    return(chunk)
  })
  combined_result = rbindlist(results)
})
stopCluster(cl)

### CALCULATE NPV GAIN AND ROTATION DIFFERENCE

combined_result = rbindlist(list(combined_result, temp_prec_period_data_x))

combined_result[, total_intensity := 1-prod(1 - exp_intensity), by = .(stand, schedule, temp, prec)]
setorder(combined_result,temp,prec,stand,schedule,period)

schedule_data = combined_result[, .(npv = first(npv), 
                                    npv_bb = first(npv*(1-total_intensity)), 
                                    harvest_period = first(harvest_period), 
                                    total_intensity = first(total_intensity)), by = .(temp,prec,stand,schedule)]

max_schedules = schedule_data[, .(sch_max_npv = schedule[which.max(npv)],
                                  sch_max_npv_bb = schedule[which.max(npv_bb)],
                                  npv_gain = max(npv_bb) - npv_bb[which.max(npv)],
                                  rotation_diff = 5*(harvest_period[which.max(npv)]-harvest_period[which.max(npv_bb)])),
                              by = .(temp, prec, stand)]

max_schedules = max_schedules[stand %in% keep_stands]

max_schedules = merge(max_schedules, stand_data, by = "stand", all.x = TRUE)

max_schedules[temp == 0.1, temp := 0]

max_schedules[, host_character := ifelse(host == 100, "Host share 100%", "Host share 50%")]
max_schedules[, density_character := ifelse(density == "High", "High density", "Low density")]
max_schedules[, age_character := ifelse(age_class == "old", "Old stands", "Young stands")]

max_schedules$host_character = factor(max_schedules$host_character, levels = c("Host share 50%", "Host share 100%"))
max_schedules$density_character = factor(max_schedules$density_character, levels = c("Low density", "High density"))
max_schedules$age_character = factor(max_schedules$age_character, levels = c("Young stands", "Old stands" ))

### SUMMARY TABLES
summary_table_1 = max_schedules[, .(mean_rot_diff = mean(rotation_diff, na.rm = TRUE),
                                               min_rot_diff = min(rotation_diff, na.rm = TRUE),
                                               max_rot_diff = max(rotation_diff, na.rm = TRUE),
                                               mean_npv_gain = mean(npv_gain, na.rm = TRUE),
                                               min_npv_gain = min(npv_gain, na.rm = TRUE),
                                               max_npv_gain = max(npv_gain, na.rm = TRUE)),
                                           by = .(host_character, density_character)]
setorder(summary_table_1,density_character,host_character)

summary_table_2 = max_schedules[, .(mean_rot_diff = mean(rotation_diff, na.rm = TRUE),
                                    min_rot_diff = min(rotation_diff, na.rm = TRUE),
                                    max_rot_diff = max(rotation_diff, na.rm = TRUE),
                                    mean_npv_gain = mean(npv_gain, na.rm = TRUE),
                                    min_npv_gain = min(npv_gain, na.rm = TRUE),
                                    max_npv_gain = max(npv_gain, na.rm = TRUE)),
                                by = .(age_character)]

summary_table_3 = max_schedules[, .(mean_rot_diff = mean(rotation_diff, na.rm = TRUE),
                                    min_rot_diff = min(rotation_diff, na.rm = TRUE),
                                    max_rot_diff = max(rotation_diff, na.rm = TRUE),
                                    mean_npv_gain = mean(npv_gain, na.rm = TRUE),
                                    min_npv_gain = min(npv_gain, na.rm = TRUE),
                                    max_npv_gain = max(npv_gain, na.rm = TRUE)),
                                by = .(si)]

max_schedules_aggregated = max_schedules[, .(npv_gain = mean(npv_gain, na.rm = TRUE),
                                             rot_diff = mean(rotation_diff, na.rm = TRUE)), 
                                 by = .(prec, temp, host_character, density_character, age_character)]

### NPV GAIN FIGURE

breaks = c(0, 0.0001, 50, 100, 250, 500, 1000, 2500, 5000, 7500, 10000, 20000, 100000)
labels = c("0", "(0-50)", paste0("[",paste(head(breaks[-c(1,2,length(breaks))], -1), tail(breaks[-c(1,2,length(breaks))], -1), sep = " - "),")"),">20000")

colors = viridis(length(breaks) - 1, option = "D")
max_schedules_aggregated$npv_gain_interval = factor(cut(max_schedules_aggregated$npv_gain, breaks = breaks, include.lowest = T, right = F))

ggplot(max_schedules_aggregated, aes(x = prec, y = temp, fill = npv_gain_interval)) +
  geom_raster() +
  scale_fill_manual(values = colors, labels = labels) +
  facet_nested(host_character ~ density_character+age_character, scales = "free") +
  theme_minimal() +
  theme(legend.position = "bottom") +
  labs(x = "Precipitation (mm)", y = "Temperature (\u00B0C)", fill = "NPV gain (NOK)")

### ROTATION DIFFERENCE FIGURE

ggplot(max_schedules_aggregated, aes(x = prec, y = temp, fill = rot_diff)) +
  geom_raster() +
  scale_fill_viridis_c() +
  facet_nested(host_character ~ density_character+age_character, scales = "free") +
  theme_minimal() +
  theme(legend.position = "bottom") +
  labs(x = "Precipitation (mm)", y = "Temperature (\u00B0C)", fill = "Rotation difference (yr)")

