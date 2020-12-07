## Welcome to FX's Pages

[1git hub 基础](https://cherchn.github.io/git_notes/)

[2自己制作的网页](https://cherchn.github.io/west/)

[3]安装R包时遇到了这样的问题（关键词：00lock）

***package ‘wordcloud’ successfully unpacked and MD5 sums checked Error in install.packages : ERROR: failed to lock directory ‘D:\Application\R-3.6.2\library’ for modifying Try removing ‘D:\Application\R-3.6.2\library/00LOCK’***

解决办法--> unlink("D:\\Program Files\\R\\R-3.6.2\\library/00LOCK", recursive = TRUE)

[4]更改某一列的列名，names(moisture_df)[names(moisture_df)=="colname"]="newname"

[5] 临时代码
getwd()
setwd("E:/master_thesis/Kmodelcalculation/metadata/NASA GLDAS Version 2 Data Products")
filelist<-list.files(path ="E:/master_thesis/Kmodelcalculation/metadata/NASA GLDAS Version 2 Data Products",pattern = "2006" )

filename<-filelist[1]
ncfile <- nc_open(filename[1])
lon <- ncvar_get(ncfile,"lon")
lat <- ncvar_get(ncfile,"lat")
moisture <- ncvar_get(ncfile,"SoilMoi0_10cm_inst")
fillvalue <- ncatt_get(ncfile,"SoilMoi0_10cm_inst","_FillValue")
moisture[moisture==fillvalue$value]<-NA
moisture_vector <- as.vector(moisture)
lonlat <- as.matrix(expand.grid(lon,lat))
moisture_df<-data.frame(cbind(lonlat,moisture_vector))
colname<-substr(filename,19,35)
names(moisture_df) <- c("lon","lat",colname)

extract_moisture <- function(mosituren_df,filelist)
  {
  
  for (i in 2:length(filelist)) {
      filename<-filelist[i]
      ncfile <- nc_open(filename)
      moisture <- ncvar_get(ncfile,"SoilMoi0_10cm_inst")
      nc_close()
      moisture[moisture==fillvalue$value]<-NA
      moisture_vector <- as.vector(moisture)#提为matrix就好
      moisture_df$colname<-moisture_vector
      colname<-substr(filename,19,35)
      names(moisture_df)[names(moisture_df)=="colname"]=colname
      }
  write.csv(moisture_df,file = "10cmhour.csv")
}

extract_moisture(mositure_df,filelist)
moisture_2006<-read.csv("10cmhour.csv")

# 每8列求一次平均值，复制进新表

daymeanvalue <- apply(moisture_2006[4:4+8],1,mean) # 平均值向量
daymean_df <- data.frame(cbind(lonlat,daymeanvalue)) # 新表
names(daymean_df) <- c("lon","lat",substr(names(moisture_200601)[4],2,9))#第一列成功 
leng <- dim(moisture_2006)[2]
for(i in seq(13,leng,by=8))
{
  daymeanvalue <- apply(moisture_200601[i:i+8],1,mean)
  daymean_df$newday <- daymeanvalue
  names(daymean_df)[names(daymean_df)=="newday"]=substr(names(moisture_200601)[i],2,9)
}
write.csv(daymean_df,file="10cmday.csv")
test<-read.csv("10cmday.csv")



