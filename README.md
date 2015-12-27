# Getting-and-Cleaning-Data
This repository contains the Getting and Cleaning Data Course Project

The repository has the run_analysis.R script. This script takes the data from the study 'Human Activity Recognition Using Smartphones Data Set' (1)

The script download the data files and unzip them. Next create a dataframe containing the mean and standar deviation of the data:

###1.Download data
###2.Unzip data
###3.Creates a vector (files) with the list of files unzipped
###4.Create one vector with de Feature.txt info
###5.Creates a vector (names) with the row names
###6.Create a DF with the activity labels
###7.Load test dataset info into data frames in R
###8.Load train dataset info into data frames in R
###9.Merge both (train and test) data frames
###10.Take the column names from the 'names' vector
###11.Select only the columns with mean or standard deviation
###12.Join the mean_std dataframe with the Activity and Subjects Dataframes
###13.Convert the Activity column in factors with de activity labels as levels
###14.Changes the variable names
###15.Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 

run_analysis.R

Data=function(){

if (!("dplyr" %in% installed.packages())) { 
  install.packages(dplyr)
}
require(dplyr)

####download data
if(!file.exists('data.zip')){
file='https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip'
download.file(file,destfile='./data.zip')
}

####Unzip data
if(!file.exists('./data')){dir.create('./data')}
unzip(zipfile='./data.zip',exdir='./data')


####Creates a vector with the list of files
files=list.files(full.names=T,recursive=T)

####Create one vector with de Feature.txt info
features=read.table(files[grep('features.txt',files)])

####creates a vector with the row names
names=as.vector(features[,2])

####Create a DF with the activity labels
activity_labels <- read.table(files[grep('labels',files)])

####load test dataset info into data frames in R
X_test=read.table(files[grep('X_test',files)])
Y_test=read.table(files[grep('/y_test',files)])
Subject_test=read.table(files[grep('subject_test',files)])


####load train dataset info into data frames in R
X_train=read.table(files[grep('X_train',files)])
Y_train=read.table(files[grep('/y_train.',files)])
Subject_train=read.table(files[grep('subject_train',files)])

####merge both (train and test) data frames
X_total=rbind(X_test,X_train) 
Y_total=rbind(Y_test,Y_train)
Subject_total=rbind(Subject_test,Subject_train)

####Take the column names from the 'names' vector
colnames(X_total)=names
colnames(Y_total)='Activity'
colnames(Subject_total)='Subject'

####select only the columns with mean or standard deviation
mean_std=X_total[,grep('std\\(\\)|mean\\(\\)',names)]

####join the mean_std dataframe with the Activity and Subjects Dataframes

Total=cbind(Subject_total,Y_total,mean_std)

####Converting the Activity column in factors with de activity labels as levels
Total$Activity=factor(Total$Activity,labels=as.vector(activity_labels[,2]))

####Changing the variable names
names(Total)<-gsub('^t','time',names(Total))
names(Total)<-gsub('^f','frequency',names(Total))
names(Total)<-gsub('Acc','Accelerometer',names(Total))
names(Total)<-gsub('Gyro','Gyroscope',names(Total))
names(Total)<-gsub('Mag','Magnitude',names(Total))
names(Total)<-gsub('BodyBody','Body',names(Total))


Total
}

Analysis=Data()

####Creates a second, independent tidy data set with the average of each variable for each activity and each subject.
TidyData<-aggregate(. ~Subject + Activity, Analysis, mean)
write.table(TidyData,file='tidydata.txt',row.name=F)





(1)Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. A Public Domain Dataset for Human Activity Recognition Using Smartphones. 21th European Symposium on Artificial Neural Networks, Computational Intelligence and Machine Learning, ESANN 2013. Bruges, Belgium 24-26 April 2013.

