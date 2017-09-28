---
layout: default
title: 'Getting Windows Uptime Using Python!'
excerpt: Using Python 3.5.1 and the built in libraries, you can get the Uptime of a Windows machine.
---
<h3>Getting Windows Uptime Using Python!</h3>
Whenever I wanted to know the uptime of a Windows machine, I had to navigate to the task manager and look at the uptime in the Performance
tab. If I wanted to know the uptime for various Windows machines, then I would have to repeat this same process a few times and spend a bit
of time doing it. I wanted to speed this process up by creating a Python script to get the uptime of a Windows machine.

The libraries that I used were:
{% highlight python %}
import subprocess
import datetime
{% endhighlight %}
 

In order to start this task, I had to figure out how to get the initial time of when my machine was booted. My research lead me to use two different commands using Windows CMD to obtain this information. They were systeminfo and net statistics server. Both commands gave me the date and time of when the machine was booted but one thing varied between them. Using the net statistics server command was the correct date and time but whenever the machine would go into sleep mode it would not update the system boot time. I initially thought that I would have to figure out how to take how long a machine would be asleep into account but the systeminfo command was one step ahead of me. It already had the amount of time the machine was asleep for and added that value to the boot time. With that information, I had chosen to use systeminfo to take the time value of the boot time as the initial value for my uptime script. To get the current time, I used two the date and time Windows commands.

In Figure 1 below, all three commands are being used in the subprocess module with Popen. The first field with Popen is used to pass the Windows command. My windows command is quite lengthy so you will need to encase the entire command within brackets. They all need to have quotation marks on them as well. Each subprocess command uses cmd and /c. The /c parameter allows the rest of the command to be executed and then terminates. The output from the command gets outputted into subprocess.PIPE which allows me to read the output. To read the output from subprocess.PIPE, you will need to use .communicate() as shown below. Once this has been done for all Windows commands, I will have to clean up the output so I can organize the data for datetime.


{% highlight python %}
current_date = subprocess.Popen(["cmd","/c","date","/T"], stdout=subprocess.PIPE)
current_date_T = current_date.communicate()
    
current_time = subprocess.Popen(["cmd","/c","time","/t"], stdout=subprocess.PIPE)
current_time_t = current_time.communicate()
    
past_time = subprocess.Popen(["cmd","/c","systeminfo","|", "find","System Boot Time:"], stdout=subprocess.PIPE)
past_system_date_time = past_time.communicate()
{% endhighlight %}
<p style="font-size:90%"> Figure 1: Subprocess command being used for systeminfo, date and time </p>


To start cleaning the output generated from the Windows commands, I had created three functions to clean them up as shown below in Figure 2. The output that was created from .communicate() had two elements inside it. The first[0] element had the information that was needed. The second element[1] had no information in it except for the statement of None. The first element was used to pass the information into the functions.

{% highlight python %}
date_tuple = cleanup_date_T(current_date_T[0])
current_time_total_minutes= cleanup_time_t(current_time_t[0])
past_date_time_tuple = cleanup_past_system_date_time(past_system_date_time[0])
{% endhighlight %}
<p style="font-size:90%"> Figure 2: Output being passed into each respective cleanup function </p>

Once the information was received to the functions, they were split using .split(). After it was split, I had to look into each element and get the necessary information that I needed such as the month,day and year for the cleaup_date_T function.

The final part to this script was to subtract the current date and time to the boot time. In order to do this, I had used the datetime library to arrange the date and time in the format of Year-Month-Day Hour-Minute. Both of these values would be arranged like this and would be subtracted from each other as shown below in Figure 3. From this value, you would get a printed message: Your machine has been online for (number) hours and (number) minutes. If the machine was on for more than a day then the output would change to: Your machine has been online for (number) day,(number) hours and (number) minutes. To confirm if the uptime from the script was correct, I had to check with task manager.

{% highlight python%}
#Organizes the date and time in this format: Year-Month-Day Hour-Minute
time_current = datetime.datetime(int(date_tuple[0]),int(date_tuple[1]),date_tuple[2], current_time_total_minutes[0],current_time_total_minutes[1])
time_past = datetime.datetime(int(past_date_time_tuple[0][2]),int(past_date_time_tuple[0][0]),int(past_date_time_tuple[0][1]),past_date_time_tuple[1],past_date_time_tuple[2])

#Subtracts the current time to the system boot time
subtraction_time = str(time_current - time_past)
time_split = subtraction_time.split(':')
print("Your machine has been online for " + time_split[0] + " hours and " + time_split[1] + " minutes.")
{% endhighlight %}
<p style="font-size:90%"> Figure 3: Datetime being utilized to format dates and subtract both dates to get uptime </p>


The full script is shown on my <a href="https://github.com/ProactiveNode/Python-and-other-scripts/blob/master/uptime.py">Github</a>
 
