---
layout: post
title: Taste of Open Access - Part 1 - How to download article meta info using Nature Open Search API
categories: []
tags: []
published: True
date: 2015-01-04 13:29:25
---

The [Open Access](http://en.wikipedia.org/wiki/Open_access) movement in the scholarly publication sector is uprising. As a researcher who has always been interested in programming (as a hobbit besides the daily work in the labs), I found it particularly interesting that many big academic journal publishers are breaking down their pay wall and starting to provide free access to all of their web contents, and some of them even provide APIs for every one to directly query their article databases. This opens up an opportunity for people like me to do some funny jobs that might not be possible before.

So, putting thoughts into actions, I pulled off a few days in the end of 2014 and wrote some codes to test what I can do with all these open information.

This [stack-exchange](http://opendata.stackexchange.com/questions/638/open-database-apis-for-journal-article-metadata) provided me with a few start points of those available APIs. First I looked into Nature's API (I did not find similar APIs provided by Science, so I just started with Nature, as we all know these two journal are the two biggest names among all). With the help of search engines I quickly landed on the documentation of its [OpenSearch](http://www.nature.com/developers/documentation/api-references/opensearch-api/) API. The APIs are quite well documented, so after several hour's keystroking I managed to select and download the meta data of the articles that I am interested in (only the abstract and some author data. At the moment of this writing the full content is not available through the API).

You can find [here](https://gist.github.com/deene/3fbb0b02fbf7c3425dbf) the code snippet that I use to download the article meta info, which were published in Nature Nanotechnology from the mid of 2006 (when this journal was established) to Dec, 2014 (the date of this writing). After running the codes in Python, a few json files, named by year, will be generated. Each json file contains a json object array of all the downloaded articles' meta info. The python script, though looks quite verbose, has only two core statements as below:

{% highlight python %}
url = 'http://api.nature.com/content/opensearch/request?&recordPacking=unpacked&queryType=cql&maximumRecords=100&httpAccept=application/json&query=' + queryWord
content = urllib2.urlopen(url).read()
{% endhighlight %}

The variable `url` is formulated according to [Nature's API protocol](http://www.nature.com/developers/documentation/api-references/opensearch-api/). And the search result is retrieved by calling the `urllib2` package function `urlopen(url)`. The rest of the code snippet is just getting the information flow in the correct form.

In the output json files store an array of all the entries found in a per-year fashion. A loop sweeping the targeted dates are constructed at the beginning of the snippet:

{% highlight python %}
# loop years
for year in range(2006, 2014):
    # setup the file to write
    fileName = 'nnano-' + str(year) + '.json'
    myfile = open(fileName, 'w+')
    yearWord = str(year)

    # loop months
    for month in range(1,13):
        if month < 10:
            monthWord =  '0' + str(month)
        else:
            monthWord = str(month)
        
        # loop days
        for day in range(1,32):
            
            # make all day number two digits
            if day<10:
                dayWord = '0' + str(day)
            else:
                dayWord = str(day)
            
            # construct the targetDate string
            targetDate = yearWord + '-' + monthWord + '-' + dayWord
{% endhighlight %}

The response from the server (`content`) of each query looks like this:

{% highlight javascript %}
{
    "comment": "nature.com OpenSearch: urn:uuid:c9532f0e-1145-4f41-962a-8021b3c90484",
    "namespaces": {
        ...
    },
    "feed": {
        ...
    }
    ...
    "sru:numberOfRecords":11,
    ...
    "entry": [
    {
        "title" : "Computing power",
        ...
    }, {
        "title" : "Top down bottom up: Shared vision",
        ...
    }, ...]
    ...
}
{% endhighlight %}

This object has a very complex structure. Luckily, there are just a few keys that matter at this stage. The `sru:numberOfRecords` key tells us how many results are found in this search, and the `entry` key contains the array of all the resulting articles if there is any. By checking these two keys, the article meta info can there for be extracted and stored in the `jsonObjArray` list:

{% highlight python %}
if data['feed']['opensearch:totalResults'] != 0:
    for entry in data['feed']["entry"]:
        jsonObjArray.append(entry)
        print "Result found"
    else:
        print "No result found!"
{% endhighlight %}

At the end of this snippet, `jsonObjArray` is dumped into a json file named by year, therefore make the data format-ready for further analysis:

{% highlight python %}
json.dump(jsonObjArray, myfile, indent=4)
{% endhighlight %}

In the next part, I will try to write about how to do simple word count on these downloaded data.

