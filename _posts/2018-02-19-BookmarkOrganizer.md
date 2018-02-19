---
layout: default
title: 'Organizing Bookmarks into Folders with Python'
excerpt: Using Python 2.7 and HTML files, you can easily organize your bookmarks based on website.
---
<h3>Organizing Bookmarks into Folders with Python</h3>
Over the years, I have bookmarked a great deal of websites and often lost track of them. I had attempted to put all of my bookmarks manually into folders but it had taken a great deal of time. In order to better organize my bookmarks and place them into folders, I had created this python script.

This was written in python 2.7. It was tested in Firefox 52.0.2 and Firefox 58.0.2. To start the process, you will need an HTML file of your bookmarks. You can obtain this file by using the keyboard shortcut CTRL+SHIFT+B for Firefox 58.0.2. In Firefox 52.0.2, you will need to use the keyboard shortcut CTRL+SHIFT+O. This will open a new window called Library. It will list all of your bookmarks and in that window there will be a drop down titled ‘Import and Backup’. Within the dropdown, there will be a button that will export your bookmarks to HTML. Once that is done, put your HTML file in the same folder as the script.

When you run the script, you will have to input the filename of the HTML file (including the extension). After that, you will have to input the website or websites that you wish to organize into folders as shown Figure 1 below. I had inputted two websites and typed in done to complete what I had wanted. 

![User inputted websites](/assets/bookmark_1.PNG)
<p style="font-size:90%"> Figure 1: User inputting the websites they want to have organized. </p>

There will be three important lists in this section:
1. Keyword - contains the websites that the user inputted
2. linesHTML - contains all the content from the bookmark HTML file
3. list_keyword - contains all the lines where the website appeared

The websites that the user inputted are placed into a list called keyword. Each element in the keyword list will be used to find the line in a list called linesHTML. linesHTML is a list that stored all the data of the bookmark HTML file. Once the website name has been found in linesHTML, it will be appended into another list called list_keyword. It will get all the occurrences of that website and be put into that list as shown in Figure 2. 

{% highlight python %}
    for g in range(len(keyword)):
          list_keyword = []
           for i in range(len(linesHTML)):
                if keyword[g] in linesHTML[i].lower():
                    list_keyword.append(linesHTML[i])
{% endhighlight %}
<p style="font-size:90%"> Figure 2: Occurences of the website getting placed into list_keyword. </p>

We will need linesHTML to construct a new HTML file and we don’t need the lines that have the website since they are stored in our list_keyword list. We will just remove the occurrences of them in linesHTML as shown below in Figure 3.

{% highlight python %}
        for p in range(len(list_keyword)):
            for z in range(len(linesHTML)):
                if list_keyword[p] in linesHTML[z]:
                    linesHTML.remove(linesHTML[z])
                    break
{% endhighlight %}
<p style="font-size:90%"> Figure 3: Occurences of the website getting removed from linesHTML. </p>

Now we have all of our data nicely organized in our list. Our final step is to put in the HTML tags that will make it into a folder. The three tags that were used were DL, p and H3. To name the folder, I simply put in the website name the user had inputted as shown below in Figure 4. The startFolder and endFolder strings were inserted at the start and end of list_keyword. List_keyword is then inserted into linesHTML and a folder is made.

{% highlight python %}
        startFolder = "<DL><p> \n <DT><H3> " + keyword[g] + "</H3> \n"
        endFolder = "</DL><p> \n"

        list_keyword.insert(0,startFolder)
        list_keyword.insert(len(list_keyword),endFolder)
        linesHTML[9:9] = list_keyword
{% endhighlight %}
<p style="font-size:90%"> Figure 4: HTML tags and data being inserted to list_keyword to create folders. </p>

After all of the folders are made, a new HTML file is created as shown below. This new HTML file will be used to import the data back into Firefox. To do this, you go back to the Library window and go to the dropdown labeled ‘Import and Backup’. In it, click ‘Import Bookmarks from HTML’. The folders will appear in your bookmarks and we have successfully organized our bookmarks.
