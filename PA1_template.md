# Reproducible Research: Peer Assessment 1
echo = TRUE  
# The code is displayed

## Loading and preprocessing the data

# Downloaded the dataset in activity.zip file and then reading it in csv format and loading it into 'activity' and checking the class

activity <- read.csv(unz("activity.zip", "activity.csv"))

sapply(activity, class)


# Process/transform the data (if necessary) into a format suitable for your analysis
# Converting activity date to date class from factor

activity$date <- as.Date(activity$date, format = "%Y-%m-%d")

# Installing the zoo package

install.packages("zoo", repos="http://R-Forge.R-project.org")

suppressWarnings(library(zoo))

# Checking whether activity$date is strictly regular and checking the unique activitiy dates

is.regular(activity$date,strict = TRUE)

unique(activity$date)

## What is mean total number of steps taken per day?

# Calculate the total number of steps taken per day

steps_day <- aggregate(steps ~ date, rm.na = TRUE, data = activity, FUN = sum)

# Checking the values stored in steps_day

steps_day

# Make a histogram of the total number of steps taken each day

plot(steps_day, type = "h", lwd = 10, lend = "square")

# Calculate and report the mean and median of the total number of steps taken per day

aggregate(steps ~ date, data = activity, FUN = mean)

aggregate(steps ~ date, data = activity, FUN = median)


## What is the average daily activity pattern?

# Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

plot(aggregate(steps ~ interval, data = activity, FUN = mean), type = "l")

# Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

max(activity$steps, na.rm = TRUE)


## Imputing missing values
# Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

sum(is.na(activity))

# Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

# The missing values can be substituted in such a way so that the Mean and Median is not affected by the substituted value.A nice way to do that is to make the substitution same as overall Mean.

# Create a new dataset that is equal to the original dataset but with the missing data filled in.

activity2 <- activity 

sapply(activity2, class)

activity2$steps[is.na(activity2$steps)] <- mean(na.omit(activity$steps))

activity2$date <- as.Date(activity2$date, format = "%Y-%m-%d")

# Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

steps_day2 <- aggregate(steps ~ date, rm.na = TRUE, data = activity2, FUN = sum)

par(mfrow = c(1, 2))

plot(steps_day, type = "h", lwd = 5,lend = "square", main = "With NAs")

abline(h = seq(0, 20000, 2500), lty = "dashed")

plot(steps_day2, type = "h", lwd = 5, lend = "square", main = "NAs filled")

abline(h = seq(0, 20000, 2500), lty = "dashed")

dev.off()


aggregate(steps ~ date, data = activity, FUN = mean)

aggregate(steps ~ date, data = activity, FUN = median)

aggregate(steps ~ date, data = activity2, FUN = mean)

aggregate(steps ~ date, data = activity2, FUN = median)

# The mean and median are getting affected due to fill in with overall mean.

## Are there differences in activity patterns between weekdays and weekends?

# Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

activity2$weekday <- factor(format(activity2$date, "%A"))

levels(activity2$weekday) <- list(weekday = c("Monday", "Tuesday",
                                              "Wednesday", "Thursday",
                                              "Friday"), weekend =
                                          c("Saturday", "Sunday"))
                                          
                                          
# Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

par(mfrow = c(2, 1))

with(activity2[activity2$weekday == "weekend",], plot(aggregate(steps ~ interval, FUN = mean), type = "l", main = "Weekends"))

with(activity2[activity2$weekday == "weekday",], plot(aggregate(steps ~ interval, FUN = mean), type = "l", main = "Weekdays"))

dev.off()
