[ ![](http://0.gravatar.com/avatar/f450c214808965f494b965d438ba780e?s=100&d=mm
&r=g) ](http://felipegalvao.com.br/blog)

# [Felipe Galvão](http://felipegalvao.com.br/blog "View all posts by Felipe
Galvão" )

##

## Published

## [maio 24, 2016](http://felipegalvao.com.br/blog/2016/05/24/web-scraping-
with-python-scraping-digital-comics-information-from-comixology/ "Web Scraping
with Python - Scraping Digital Comics information from Comixology" )

Skip to content

  * [Data Science / Analysis with Python - Basics](http://felipegalvao.com.br/blog/data-science-analysis-with-python-basics/)
  * [Ciência / Análise de Dados com Python - Básico](http://felipegalvao.com.br/blog/ciencia-de-dados-com-python-basico/)
  * [Contatos / Contact Me](http://felipegalvao.com.br/blog/contatos-contact-me/)

##  [Felipe Galvão](http://felipegalvao.com.br/blog "View all posts by Felipe
Galvão" ) in [Python (English)](http://felipegalvao.com.br/blog/category
/python-english/ "View all posts in Python \(English\)" ) |  [maio 24,
2016](http://felipegalvao.com.br/blog/2016/05/24/web-scraping-with-python-
scraping-digital-comics-information-from-comixology/ "Web Scraping with Python
- Scraping Digital Comics information from Comixology" )

原文：[Web Scraping with Python - Scraping Digital Comics information from Comixology](http://felipegalvao.com.br/blog/2016/05/24/web-scraping-with-python-scraping-digital-comics-information-from-comixology/)

---

## Introduction

Well, after having talked about the previous analysis that I did about
Comixology in the [previous post in this
series](http://felipegalvao.com.br/blog/2016/05/01/comixology-analise-de-
quadrinhos-digitais-parte-1-analise-do-site-para-web-scraping/), we'll now
talk about the web scraping with Python, the code itself. As we have already
talked about, we will use requests to make requests to web pages and lxml with
Xpath for parsing and information extraction from the desired pages. Let's go.

## Starting to write the code

To start the conversation, we will import the necessary libraries, requests
and, from lxml, the html function. To access the HTML parts, we will use
Xpath. So, let's talk about Xpath before we move on to the code. I'll try to
keep it short.

## Xpath: What it is and how to use it

Xpath is a query language used to extract content from XML / HTML documents.
It can be used to extract information form nodes and attributes, being very
useful in web scraping tasks. Xpath uses sentences that match elements in the
HTML or XML code.

XML / HTML documents are treated as node trees. The nodes can have
relationships, possibly being fathers, childrens, siblings, ancestors and
descendants. Considering the following XML code:


```python

    <store>

      <product>

        <class>Shirt</class>

        <color>Red</color>

        <price>25.00</price>

      </product>

    </store>
```

The relations are as following:

  * Father - Elements that contain another elements. In our example, product is the father of class, color and price.
  * Children - Analogously, children are elements that are contained in another element. The class, color and price elements are children of the product element.
  * Siblings - Elements inside of the same node. Class, color and price are siblings.
  * Ancestors - All elements that contain a certain element, like the father of an element, the father of the father, and so on. In our example, product and store are ancestors of the color, or ancestor of the class.
  * Descendants - Elements contained in another element, independent of the level. Like children, children of the children. Class, color and price are descendants of the store element.

To select nodes / elements, we use some symbols to construct expressions that
reach the desired information in the XML / HTML code. A symbol has a function,
as I show in the table below:

Symbol | Function  
---|---  
name | Select any element with this name.  
/ | Selects from a root element.  
// | Selects elements that match the selection criteria, no matter where they
are.  
. | Selects the current element.  
.. | Selects the father of the current element.  
@ | Selects an attribute.  
  
In this way, we can build an expression that selects the elements where the
information we desire is. Let's look at an example with HTML code:


```python

    <html>

      <body>

        <div class="style-div">

          <p>Text 1</p>

        </div>

        <div class="style-div">

          <p>Text 2</p>

        </div>

        <div>

          <p id="p-paragraph">Text 3</p>

        </div>

      </body>

    </html>
```

Considering this code and the elements we already talked about, we have some
examples:

//div | Select all the div elements in the document.  
---|---  
/html | Select the root element html.  
html//p | Select any p element inside the html element, no matter where they
are.  
html/div | Select the div elements that are children of the html element.  
  
You can also combine elements and attributes for the selection. To extract the
text inside elements, we use text() in the end, and this is one of the
features that we will use the most. Let's see:

//@class | Select all the class attributes in the document.  
---|---  
//div[@class='style-div']/p | Select all the p elements that are children of
div elements with a class value of "style-div".  
//p[@id='p-paragraph']/text() | Select the text inside each p element with an
id value of "p-paragraph".  
  
I uploaded a simple HTML file so we can construct the code in Python and check
these examples in practice. The address of the page is:
<http://felipegalvao.com.br/scraping/>

And now we will go to the scraping code for these simple examples:


```python

    import requests

    from lxml import html

    

    page = requests.get('http://felipegalvao.com.br/scraping-en')

    tree = html.fromstring(page.content)

    

    example1 = tree.xpath('//p/text()')

    print(example1)

    _['Text 1', 'Text 2', 'Text 3']_

    

    example2 = tree.xpath('/html/body/div/p/text()')

    print(example2)

    _['Text 1', 'Text 2', 'Text 3']_

    

    example3 = tree.xpath('//div[@class="style-div"]/p/text()')

    print(example3)

    _['Text 1', 'Text 2']_

    

    example4 = tree.xpath('//p[@id="p-paragraph"]/text()')

    print(example4)

    _['Text 3']_

    

    example5 = tree.xpath('//p[@id="p-paragraph-foo"]/text()')

    print(example5)

    _[]_
```

In short, first we make the request, passing the address to be scraped to the
requests.get function. Then, we use the html() function from lxml to extract
the HTML source code from the address. Finally, we use the xpath function to
extract the information using sentences created with the syntax that we just
saw.

As it is possible to note, the returned value is always a list, and we just
need to treat it like a normal Python list. Also note that, when we search for
something that does not exist in the document, an empty list is returned.

And now that we saw this simple example, let's move on to something more
complex and fun.

## Returning to the code

Now that we know what Xpath is about, let's use it to extract the Comixology
website itself. For web scraping with Xpath, one of our best friends will be
the "Inspect Element" feature from Chrome or Firefox. It will allow us to see
the source code and the HTML structure and path for a certain element in the
page.

Let's move on to the Publishers section on Comixology. ([click
here](https://www.comixology.com/browse-publisher)). We will extract our code
defining the function and creating an empty list which will hold the Publisher
links that we will extract. As we talked on the first post, we will define the
page quantity manually (4 pages, so, we will have a for page in range (1,5)),
and we will pass through each of them. If we go to the site and change to the
next page, we will see that the link becomes: https://www.comixology.com
/browse-publisher?publisherList_pg=2. If we change it back to the first page,
the final part of the link turn back to 1. The first pages of the code will be
as following:


```python

    from lxml import html

    import requests

    

    def get_publishers_links():

        publisher_links = []

        

        # Iterate through each page of the Browse > By Publisher link

        for i in range(1,5):

            # Define the link to be explored

            link = 'https://www.comixology.com/browse-publisher?publisherList_pg='

            page = requests.get(link+str(i))

            tree = html.fromstring(page.content)
```

Simple stuff until now. We need to find the quantity of Publishers in each
page. For this, let's inspect the element that constitutes a Publisher. It
could be the image, the link. Also, we must not forget that we will not
consider the Featured Publishers table. When we inspect an element of the All
Publishers table and compare it with an element of the Featured Publishers
table, we note that an intermediate div has a class that differentiate them.
While items from the Featured Publishers table are inside a div that has the
class "list FeaturedPublisherList", items from the All Publishers table are in
a div that has the class "list publisherList". Let's make our Xpath sentence
going from there:

[![comixology_2_1-publishers](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_1-publishers-
1024x925.jpg)](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_1-publishers.jpg)

Now, I'll create a Xpath string for the extraction of the elements, going from
the div with the class that we just saw (//div[@class="list publisherList"]),
going through the HTML elements until we get to the <a>, where we will extract
the "href" attribute, which is the link itself. I'll divide the creation of
the string in 2 lines, so that we won't have a very long line. An important
detail that I haven't mentioned until now is that the extracted links come
with a ref attribute. We will create a function to remove this attribute and
return a list of "clean" links. We will pass our list of extracted links to
this function. We will then use the extend function to put this list inside
the empty list that we create on the start of our scraping function. When we
do this for every page, we will have every Publisher link. The function that
will remove the attribute will use Regex (regular expressions; we will need to
import the re function to work with Regex in Python), which I'll not explain
on this post, but basically, we will replace everything from the "?",
including the "?", by nothing. Let's see how everything looks:


```python

    from lxml import html

    import requests

    import re

    

    # Remove attributes from links in a list and return the "clean" list

    def remove_attributes_from_link(comic_link_list):

        clean_comic_link_list = []   

        for comic_link in comic_link_list:

            # Replace everything after the "?" by nothing

            new_comic_link = re.sub("(\?.+)","",comic_link)

            clean_comic_link_list.append(new_comic_link)

        return(clean_comic_link_list)

    

    # Return a list with links to the Publishers

    def get_publishers_links():

        publisher_links = []

        

        # Iterate through each page of the Browse > By Publisher link

        for i in range(1,5):

            # Define the link to be explored

            link = 'https://www.comixology.com/browse-publisher?publisherList_pg='

            page = requests.get(link+str(i))

            tree = html.fromstring(page.content)

            

            # Define the Xpath expression for the extraction of Publishers links

            quantity_pub_xpath = '//div[@class="list publisherList"]/ul'

            quantity_pub_xpath += '/li[@class="content-item"]/figure/div/a/@href'

            

            # Extract the links through Xpath

            extracted_publishers_links = tree.xpath(quantity_pub_xpath)

            clean_publisher_links = remove_attributes_from_link(

                extracted_publishers_links)

            # Insert the links in the created list

            publisher_links.extend(clean_publisher_links)

                

        return(publisher_links)
```

The comments will help you understand each part of the code. If everything
goes right, this function will extract all the links for the Publishers, and
you will have them on the list returned by the function. First part, concluded
🙂

I'll take this time to talk about a library that will help us in this task,
which is the Pickle package ([to know more about it, click
here](https://wiki.python.org/moin/UsingPickle)). This library will allow us
to export data to files, to load them back later on. In this first moment,
this will not seem very useful, since this extraction function runs very fast
and does not take much time to be completed, because there are only 4 pages to
extract. But for the next steps, when we have lots of pages and links to
visit, this package will be extremely important. Let's export our list of
links to a file. We will use the dump function from the pickle package,
passing to it the object to be exported:


```python

    import pickle

    

    publishers_links = get_publishers_links()

    pickle.dump(publishers_links, open("publishers_links.p","wb"))
```

To load back the exported object, we use the load() function from pickle,
passing to it the desired file:

```python

    publishers_links = pickle.load(open("publishers_links.p","rb"))
```

## From Publishers to Series

Now that we already have the Publishers links, our next step is similar. Now,
we need to define a function that will receive a list of Publisher links, go
through each of these links, extracting the Comics Series links and exporting
them to a file that we can load later on. Let's start:


```python

    def get_series_links_from_publisher(publisher_links):

        series_links = []

        

        for link in publisher_links:

            page = requests.get(link)

            tree = html.fromstring(page.content)
```

Up until there, all simple. After that, we have to discover the number of
pages. When we explore some Publisher links, we note that there are three
possible cases: A - when the publisher has only one page, B - when there is a
small number of pages (like, from 2 to more or less 10), C - when a publisher
has a great number of pages. See in the image:

[![A - Publisher with 1 page](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_2-publisher_no_pages-
300x238.png)](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_2-publisher_no_pages.png)

A - Publisher with 1 page

|

[![B - Publisher with few pages](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_3-publisher_few_pages-
208x300.png)](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_3-publisher_few_pages.png)

B - Publisher with few pages

|

[![C - Publisher with many pages](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_4-publisher_many_pages-
228x300.png)](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_4-publisher_many_pages.png)

C - Publisher with many pages  
  
---|---|---  
  
With this scenario, it is a little harder to get the number of pages directly.
But there is an easy way. When there is more than one page, the total number
of Comics Series is shown in the lower right corner of the Comics Series
table. Since one page always have 36 Series (with the exception being the last
one, or ir the Publisher has only one page), the number of pages is an easy
calculation. If the page is divisible by 36, the number of pages is equal to
the quantity of Series divided by 36, in Integer division (in Python 3, use
"//"). If the number of Series is not divisible by 36, all we got to do is add
1 to the result of the previous division. If the number of Series is not
available, the Publisher has only one page of Series. Simple, isn't it? Well,
let's inspect some elements to see how we are going to build the Xpath
sentence, and then we can go to the code:

[![Path to quantity of Series](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_5-series_pages-
1024x469.jpg)](http://felipegalvao.com.br/blog/wp-
content/uploads/2016/05/comixology_2_5-series_pages.jpg)

Path to quantity of Series


```python

    # Xpath string for extraction of total quantity of Series

    xpath_series = '//div[@class="list seriesList"]/div[@class="pager"]'

    xpath_series += '/div[@class="pager-text"]/text()'

    

    total_series = tree.xpath(xpath_series)

    

    # If the extraction returned the total quantity

    if total_series:

        # The only item in the list is a string with the quantity, which

        # we will split to create a list with each word of it            

        total_series = total_series[0].split()

        # Quantity of series will be the last item of that series

        total_series = int(total_series[len(total_series)-1])

        # Divide the quantity of Series by 36, in order to discover the 

        # number of pages of Series in this Publisher

        if total_series % 36 == 0:

            number_of_pages = (total_series // 36)

        else:

            number_of_pages = (total_series // 36) + 1

    # If the extraction returns an empty list, there is only one page of Series

    else:

        number_of_pages = 1
```

The split function will divide the string we extracted to a list, where the
last item will be the total of Series from this Publisher. As we already
talked about, if the Xpath does not find anything, the Publisher has only one
Series.

Now that we have the number of pages, we can go through them, extracting the
links from each Series and move on to the next page, until there are no more
pages. The final function will be like this:


```python

    # Receive list of links for each Publisher and return list of links for the 

    # Comics Series

    def get_series_links_from_publisher(publisher_links):

        series_links = []

        

        for link in publisher_links:

            page = requests.get(link)

            tree = html.fromstring(page.content)

            

            # Xpath string for extraction of total quantity of Series

            xpath_series = '//div[@class="list seriesList"]/div[@class="pager"]'

            xpath_series += '/div[@class="pager-text"]/text()'

            

            total_series = tree.xpath(xpath_series)

            

            # If the extraction returned the total quantity

            if total_series:

                # The only item in the list is a string with the quantity, which

                # we will split to create a list with each word of it

                total_series = total_series[0].split()

                # Quantity of series will be the last item of that series

                total_series = int(total_series[len(total_series)-1])

                # Divide the quantity of Series by 36, in order to discover the

                # number of pages of Series in this Publisher

                if total_series % 36 == 0:

                    number_of_pages = (total_series // 36)

                else:

                    number_of_pages = (total_series // 36) + 1

            # If the extraction returns an empty list, there is only one page of Series

            else:

                number_of_pages = 1

            for page_number in range(1,number_of_pages+1):

                page = requests.get(link+'?seriesList_pg='+str(page_number))

                tree = html.fromstring(page.content)

                

                # Xpath for extraction of the Series links in this page

                xpath_series_links = '//div[@class="list seriesList"]/ul/'

                xpath_series_links += 'li[@class="content-item"]/figure/'

                xpath_series_links += 'div[@class="content-cover"]/a/@href'

                extracted_series_links = tree.xpath(xpath_series_links)

                clean_series_links = remove_attributes_from_link(extracted_series_links)

                series_links.extend(clean_series_links)

        return(series_links)
```

Again, we will use pickle to export our list of links in a file. This step
should take longer than the first one to complete, but it should not take more
than some minutes.

PS: The exporting will be essential on the next steps of our scraping, since
unexpected errors may cause lots of work to be wasted, so, it is useful to
export periodically our data in order to avoid that.

```python

    pickle.dump(series_links, open("series_links.p","wb"))
```

## From series to Comics

Now, we have to take one more step to go through Series links and extract
links for the comics itself. Prepare yourselves, cause the execution of this
step will take some time, due to the long list of links to visit. However, the
idea is basically the same: go through each page extracting each of the links
we desire. As we already saw on our previous post, the analysis of the
website, we will have to extract links for different types / categories of
comics, like Collected Editions, Issues, etc. Let's consider that Series links
are in the variable "series_links", according to the previous part of the
post, and let's start our code:


```python

    def get_issues_links_from_series(series_links, comics_links_counter):

        comics_links = []

        

        for counter, link in enumerate(series_links):

            page = requests.get(link)    

            tree = html.fromstring(page.content)
```

For the next step, we will create another function, since the blocks are very
similar, with only a few changes with respect to links and path of the XPath
for the div that contains certain type of comic. But first, to understand
well, we will make the code for a specific block. Then we shall see the parts
of the code that must be repeated and how we can structure our function.

Let's start the first block, scraping the Collected Editions links. The first
thing we'll do is check if this block actually exists for the current Series.
Not all series have all types of comics, and in fact, most of them only has
the Issues block. Thus, if the block does not exists, we do not need to
perform this extraction code. We will inspect the element to understand the
structure that contains the links that we want, and then, via XPath, we will
see if this structure exists:

[![Comixology - Series - Collected Editions
div](http://felipegalvao.com.br/blog/wp-content/uploads/2016/05
/comixology_2_X-series_collected-
1024x351.jpg)](http://felipegalvao.com.br/blog/wp-content/uploads/2016/05
/comixology_2_X-series_collected.jpg)

Comixology - Series - Collected Editions div


```python

    # Div where the Collected Editions are 

    collected_div = tree.xpath('//div[@class="list CollectedEditions"]')

            

    if collected_div:
```

As we have seen in the image, the structure that we seek is a div with the
class attribute = "list CollectedEditions". Knowing that, we check if this div
exists through XPath and, if it does, continue to execute our code.

As each type of comic may have one or more pages, we follow the same logic
that we have used for the Series. Let's see if there is the total number of
that type of comic. If the number is present, such amount divided by 18, which
is the amount of comics for each page of a given type. If the amount of that
kind of comic is not on the page, it means that this type has only one page.


```python

    # Path to total Collected Editions comics

    xpath_total_collected = '//div[@class="list CollectedEditions"]/'

    xpath_total_collected += 'div[@class="pager"]/'

    xpath_total_collected += 'div[@class="pager-text"]/text()'

    total_collected = tree.xpath()

    if total_collected:

        total_collected = total_collected[0].split()

        total_collected = int(total_collected[len(total_collected)-1])

        if total_collected % 18 == 0:

            number_of_pages = (total_collected // 18)

        else:

            number_of_pages = (total_collected // 18) + 1

    else:

        number_of_pages = 1
```

Now we go to the end of the block, scraping itself. We iterate through each
page (if more than one) or access the unique page for that type of comic.
Let's extract the links of this type, referencing the correct div, pass these
links thorugh our function that removes attributes of links and add them to
the list that we have created before to store links:


```python

    for page_number in range(1,number_of_pages+1):

        page = requests.get(link+'?CollectedEditions_pg='+str(page_number))                

        tree = html.fromstring(page.content)

        # Path for the Xpath with links to Collected Editions on the current page

        collected_links_xpath = '//div[@class="list CollectedEditions"]/ul/li/figure/'

        collected_links_xpath += 'div/a/@href'

        collected_links = tree.xpath(collected_links_xpath)

        clean_collected_links = remove_attributes_from_link(collected_links)            

        comics_links.extend(clean_collected_links)
```

Just to remember, that variable link is the link of the series, which is set
in the for block.

Let's analyze this piece of code and see what would change for the other
blocks. Below is the complete code:


```python

    collected_div = tree.xpath('//div[@class="list CollectedEditions"]')

            

    if collected_div:

        xpath_total_collected = '//div[@class="list CollectedEditions"]/'

        xpath_total_collected += 'div[@class="pager"]/'

        xpath_total_collected += 'div[@class="pager-text"]/text()'

        total_collected = tree.xpath()

        if total_collected:

            total_collected = total_collected[0].split()

            total_collected = int(total_collected[len(total_collected)-1])

            if total_collected % 18 == 0:

                number_of_pages = (total_collected // 18)

            else:

                number_of_pages = (total_collected // 18) + 1

        else:

            number_of_pages = 1

        for page_number in range(1,number_of_pages+1):            

            page = requests.get(link+'?CollectedEditions_pg='+str(page_number))                

            tree = html.fromstring(page.content)

            # Path for the Xpath with links to Collected Editions on the current page

            collected_links_xpath = '//div[@class="list CollectedEditions"]/ul/li/figure/'

            collected_links_xpath += 'div/a/@href'

            collected_links = tree.xpath(collected_links_xpath)

            clean_collected_links = remove_attributes_from_link(collected_links)            

            comics_links.extend(clean_collected_links)
```

Evaluating the code you can note that only two things will change. The piece
that makes up the link with the page (in the case above, '?
CollectedEditions_pg =') and the paths to the XPath. For the Xpath paths,
additionally, we note inspecting elements on the page that what changes is
only div class, and all other paths start from it, remaining unchanged from
type to type of comic. Therefore, our function will need two informations,
which are the way of Xpath to the div and the part of the link to be accessed,
corresponding to this type of comic. In addition, we will also provide the
tree object and the current link, so you do not need to make any request again
unnecessarily. Our function, then, looks like this:


```python

    def extract_comics_links(link, div_xpath, page_link_str, tree):

        type_comics_links = []

        # Check if the div for this type of comic exists

        type_div = tree.xpath(div_xpath)

        

        if type_div:

            # Get the total quantity of comics for this type of comic

            total_quantity_xpath = div_xpath + '/div[@class="pager"]/'

            total_quantity_xpath += 'div[@class="pager-text"]/text()'

            total_type = tree.xpath(total_quantity_xpath)

            if total_type:

                total_type = total_type[0].split()

                total_type = int(total_type[len(total_type)-1])

                if total_type % 18 == 0:

                    number_of_pages = (total_type // 18)

                else:

                    number_of_pages = (total_type // 18) + 1

            else:

                number_of_pages = 1            

            for page_number in range(1,number_of_pages+1):            

                page = requests.get(link+page_link_str+str(page_number))                

                tree = html.fromstring(page.content)

                # Path for the links to this type of comic

                type_links_xpath = div_xpath + '/ul/li/figure/div/a/@href'

                type_links = tree.xpath(type_links_xpath)

                clean_type_links = remove_attributes_from_link(type_links)            

                type_comics_links.extend(clean_type_links)

        return(type_comics_links)
```

This function returns a list of all links to this particular type of comic.
Now all we need to do is to repeat this function for each type with the piece
of the link and the path to the appropriate div, which we discover inspecting
each type of comic.

The final code for this function will be like below:


```python

    def get_issues_links_from_series(series_links):

        comics_links = []

        

        for counter, link in enumerate(series_links):

            page = requests.get(link)    

            tree = html.fromstring(page.content)

            

    # -------------------------------------------------------------- #

            # Scraping code for the collected editions

    # -------------------------------------------------------------- #        

            

            collected_div_xpath = '//div[@class="list CollectedEditions"]'

            collected_link_str = '?CollectedEditions_pg='        

                        

            collected_links = extract_comics_links(link, collected_div_xpath, 

                                                   collected_link_str, tree)

                    

    # -------------------------------------------------------------- #

            # Scraping code for the Issues

    # -------------------------------------------------------------- #

            issues_div_xpath = '//div[@class="list Issues"]'

            issues_link_str = '?Issues_pg='

                        

            issues_links = extract_comics_links(link, issues_div_xpath,

                                                issues_link_str, tree)

                    

    # -------------------------------------------------------------- #

            # Scraping code for the Omnibuses

    # -------------------------------------------------------------- #

            omnibuses_div_xpath = '//div[@class="list Omnibuses"]'

            omnibuses_link_str = '?Omnibuses_pg='               

                        

            omnibuses_links = extract_comics_links(link, omnibuses_div_xpath, 

                                                   omnibuses_link_str, tree)

            

    # -------------------------------------------------------------- #

            # Scraping code for the One-Shots

    # -------------------------------------------------------------- #

            oneshots_div_xpath = '//div[@class="list OneShots"]'

            oneshots_link_str = '?Oneshots_pg='

                        

            oneshots_links = extract_comics_links(link, oneshots_div_xpath,

                                                  oneshots_link_str, tree)

                        

    # -------------------------------------------------------------- #

            # Scraping code for the Bandees Dessinees

    # -------------------------------------------------------------- #

            bandes_div_xpath = '//div[@class="list BandesDessines"]'

            bandes_link_str = '?BandeesDessinees_pg='

                        

            bandes_links = extract_comics_links(link, bandes_div_xpath,

                                                  bandes_link_str, tree)

    

    # -------------------------------------------------------------- #

            # Scraping code for the Graphic Novels

    # -------------------------------------------------------------- #

            graphicnovels_div_xpath = '//div[@class="list GraphicNovels"]'

            graphicnovels_link_str = '?GraphicNovels_pg='

                        

            graphicnovels_links = extract_comics_links(link, graphicnovels_div_xpath,

                                                  graphicnovels_link_str, tree)

    

    # -------------------------------------------------------------- #

            # Scraping code for the Extras

    # -------------------------------------------------------------- #

            extras_div_xpath = '//div[@class="list Extras"]'

            extras_link_str = '?Extras_pg='

                        

            extras_links = extract_comics_links(link, extras_div_xpath,

                                                  extras_link_str, tree)

                        

    # -------------------------------------------------------------- #

            # Scraping code for Artbooks

    # -------------------------------------------------------------- #

            artbooks_div_xpath = '//div[@class="list Artbooks"]'

            artbooks_link_str = '?Artbooks_pg='

                        

            artbooks_links = extract_comics_links(link, artbooks_div_xpath,

                                                  artbooks_link_str, tree)

                                                  

            # Add links to all kinds of comics in the list previously created

            comics_links.extend(collected_links + issues_links + omnibuses_links + 

                                oneshots_links + bandes_links + graphicnovels_links + 

                                extras_links + artbooks_links)

            

            # Export links each time 100 links are visited, avoiding loss of information 

            # of possible errors

            if counter % 100 == 0 and counter != 0:

                pickle.dump(comics_links, open("comics_links_files/comics_links" + "_" + 

                            str((counter + series_current_counter) // 100) + ".p","wb"))

                pickle.dump(counter + series_current_counter, 

                            open("comics_links_files/series_current_counter.p","wb"))

                comics_links = []

            if counter == len(series_links) - 1:

                pickle.dump(comics_links, open("comics_links_files/comics_links" + "_" + 

                            str((counter + series_current_counter) // 100 + 1) + ".p","wb"))

                pickle.dump(counter + series_current_counter, 

                            open("comics_links_files/series_current_counter.p","wb"))

                comics_links = []

        return(comics_links)
```

Here, we use pickle.dump to export the links that we already extracted for
files each time the counter hits a multiple of 100. We do this because, with
the amount of links that we have to see, it is quite possible that a
connection problem occurs, the site goes offline for a while, or anything of
the sort. Any of these errors can make so that all the information generated
by the code is lost. So, we export the information periodically. The code also
exports the counter where the last export occurred. This way, when we call the
function, our code can check if there are already exported data, and, through
the counter value, pick up where we left off.

What we can also do is, seeing how the exporting of the links and the counter
repeats itself, is transform this part of the code in one more function.


```python

    def comics_links_dump(comics_links, counter, comics_links_counter):

        pickle.dump(comics_links, open("comics_links_files/comics_links" + 

                    "_" + str((counter + comics_links_counter) // 100 + 1) 

                    + ".p","wb"))

        pickle.dump(counter + comics_links_counter, 

                    open("comics_links_files/comics_links_counter.p","wb"))

        comics_links = []

        return(comics_links)
```

And the final part of the function will be like this:


```python

    # Export comics links each time the counter hits a multiple of 100 or when it 

    # reaches the last one, to avoid loss of information in possible errors        

    if (counter % 100 == 0 and counter != 0) or (

    	counter == len(series_links) - 1):

    	comics_links = comics_links_dump(comics_links, counter,comics_links_counter)
```

And finally, to the last step! The extraction of information from each comic
link.

## The last step: extraction of information from comics

The last step is relatively simple. We'll have to go into each comic link and
extract all the information that is possible from these links. One of the
things I learned while doing the scraping is that comics can be removed from
the site. Thus, when trying to make a request, we will return a 404 error
page, and nothing of our scraping code will work. We will first extract the
title of the HTML document to see if we are in an error page. We set the base
path from which we retrieve all the information. Our information will be all
in a dictionary, which will later be included in a list. Each key of the Dict
will be a comic information. With this configuration, Pandas lets you easily
create a Dataframe from a list of Dictionaries. From here, our task basically
boil down to inspect elements through the browser and set the corresponding
Xpath in the code. Let's go:


```python

    def get_comic_info_from_page(link):

        comic_info = {}    

        

        page = requests.get(link)    

        tree = html.fromstring(page.content)    

        

        # Define the base path that contains the content of the page

        base_path = '//div[@class="comic_view detail-container"]'

        

        # Extract the title of the page, to check if this address returns a 404 error

        page_title = tree.xpath('//title/text()')[0]    

        if page_title != 'Site Error - Comics by comiXology':

            # Extract the name of the comic

            name = tree.xpath('//h2[@itemprop="name"]/text()')

            comic_info['Name'] = name[0]
```

And so, we extract the first information about the comic. In the next steps,
we will fix some names that are hidden in the comic credits, in the sidebar to
the right of the page, and we will use Regex to remove some HTML escape
sequences that are extracted. Here, the information available varies from
comic to comic. So our code will take the name of each information of credits
(by Art, by Written, etc. - this is the XPath defined in credits_tasks
variable) and what is its value (the people themselves - XPath defined in the
variable_names credits). Let's continue:


```python

            # Extract list of tasks from credits and names that execute each task

            credits_tasks_str = base_path + '/div[@id="column3"]/'

            credits_tasks_str += 'div[@class="credits"]/div/dl/dt/text()'

            credits_tasks = tree.xpath(credits_tasks_str)

            

            credits_names_str = base_path + '/div[@id="column3"]/'

            credits_names_str += 'div[@class="credits"]/div/dl/dd/a/text()'        

            credits_names = tree.xpath(credits_names_str)

            

            # ---------------------------------------------------------------------

            # Fix names, remove scape sequences and create new list of names

            # ---------------------------------------------------------------------

            credits_names_lists = []

            

            first_item = 0

            for counter, name in enumerate(credits_names):

                if name == 'HIDE...':

                    credits_names_lists.append(credits_names[first_item:counter])

                    first_item = counter+1

            

            new_names = []

            new_names_credits = []

                    

            for names_list in credits_names_lists:

                for name in names_list:

                    new_name = re.sub("^\\n\\t\\t\\t\\t\\t\\t\\t","", name)

                    new_name = re.sub("\\t\\t\\t\\t\\t\\t$", "", new_name)

                    if new_name != "More...":

                        new_names.append(new_name)

                new_names_credits.append(new_names)

                new_names = []

            # ---------------------------------------------------------------------

            # End of name fixing

            # ---------------------------------------------------------------------

            

            # Insert each credits information in the comic_info dictionary

            for counter, item in enumerate(credits_tasks):        

                comic_info[item] = new_names_credits[counter]
```

If you look at the source code, in the part where the credits are located, all
lists have an item named "HIDE …", which is hidden. In this way, we make our
for block go up to this item, and when we get it, we add the names to the list
at that point, which is he index where the item "HIDE …" is. In the end, we
just add everything to the Dictionary.

Now, let's extract other Comic information such as page count, Publisher,
among others. This information can be found under credits, and we extract them
all at once. The name of each information is within an h4 element with class
"subtitle" and values are within a div element with class "aboutText". Let's
create the full path collecting information from other places.


```python

            # Extract Publisher of comic

            publisher = tree.xpath('//*[@id="column3"]/div/div[1]/a[2]/span/text()')

            publisher = re.sub("^\\n\\t\\t\\t","",publisher[0])

            publisher = re.sub("\\t\\t$","",publisher)

            comic_info['Publisher'] = publisher

            

            # Extract list of informations of the comic, such as page count, 

    		# age classification, etc        

            comics_infos_names_xpath = base_path + '/div[@id="column3"]/'

            comics_infos_names_xpath += 'div[@class="credits"]/'

            comics_infos_names_xpath += 'h4[@class="subtitle"]/text()'

            comics_infos_names = tree.xpath(comics_infos_names_xpath)

            

            comics_infos_values_xpath = base_path + '/div[@id="column3"]/'

            comics_infos_values_xpath += 'div[@class="credits"]/'

            comics_infos_values_xpath += 'div[@class="aboutText"]/text()'        

            comics_infos_values = tree.xpath(comics_infos_values_xpath)

            

            # Add the information of the comic into the dictionary

            for counter, item in enumerate(comics_infos_names):

                if item == 'Page Count':            

                    comic_info[item] = int(comics_infos_values[counter].split()[0])

                else:

                    comic_info[item] = comics_infos_values[counter]
```

Now, to get the price, we have to consider three situations. The first, most
common, are comics with a fixed price and no discount. The second is
discounted comics. And the third, free comics. The way I found to organize
this situation was to use three fields, an original price, other final price
and other of Discount (this one is a boolean that indicates whether a comic is
discounted or not). Where there is no discount, the original price and the
final price are equal. We also treat in our code some comics that are unique
to bundles, which then become valueless.


```python

            # Extract prices from the comic

            full_price_xpath = '//h6[@class="item-full-price"]/text()'

            # Extract full price

            full_price = tree.xpath(full_price_xpath)

            discounted_price_xpath = '//div[@class="pricing-info"]/'

            discounted_price_xpath += 'h5[@class="item-price"]/text()'

            # Extract discounted price, if it exists

            discounted_price = tree.xpath(discounted_price_xpath)

            if discounted_price:

                # If discounted price is equal to the string FREE, this is a free comic

                if discounted_price[0] == 'FREE':

                    final_price = 0.0

                # If it is not, extract the final price

                else:

                    final_price = float(discounted_price[0][1:])

                # If there is a full price, the comic has a discounted price too

                if full_price:

                    original_price = float(full_price[0][1:])

                    discounted = True

                # If there is not, the prices are equal and there is no discount

                else:

                    original_price = final_price

                    discounted = False            

            # Comics exclusive to bundles

            else:

                final_price = None

                original_price = None

                discounted = False

            comic_info['Original_price'] = original_price

            comic_info['Final_price'] = final_price

            comic_info['Discounted'] = discounted
```

Finally, we extract the average rating received by that comic and the amount
of reviews that it has. At first it seemed that I would have to count the
number of classes that determined a rating, but with a little inspection and
viewing the source code, I found that there was a hidden element with the
rating value. Then, it became easy. The quantity of ratings is also simple, as
there is a clear element that contains this number. Let's see:


```python

            # Extract comic rating from the hidden element

            ratings_value_xpath = '//*[@id="column2"]/div[2]/div[2]/div[2]/text()'

            ratings_value = tree.xpath(ratings_value_xpath)

            if ratings_value:

                ratings_value = ratings_value[0]

                ratings_value = re.sub("^\\n\\t\\t\\t\\t\\t\\t\\t","",ratings_value)

                ratings_value = int(re.sub("\\t\\t\\t\\t\\t\\t$","",ratings_value))

                comic_info['Rating'] = ratings_value

            else:

                comic_info['Rating'] = None

            

            # Extract comic's rating quantity

            ratings_quantity_xpath = '//*[@id="column2"]/div[2]/div[2]/div[1]/text()'

            ratings_quantity = tree.xpath(ratings_quantity_xpath)    

            if ratings_quantity:

                ratings_quantity = ratings_quantity[0].split()[2]

                ratings_quantity = ratings_quantity[1:][:-2]        

                comic_info['Ratings_Quantity'] = int(ratings_quantity)

            else:

                comic_info['Ratings_Quantity'] = 0
```

And so, we complete the information gathering functions. Now, let's set a few
things so that they all get linked.

## Final Touches

We will need three more functions. The first will simply join the comics of
the links, which are scattered in files, in a single list. The second function
receives the comics list that we already extracted and will go through each
link extracting information and then export this information to files. And the
last will gather the information from these files into another new variable,
so we can finally use them for analysis. Let's go to the first function:


```python

    def join_comics_links():

        comics_links = []

        # For each file one the "comics_links_folder" folder

        for file in os.listdir("comics_links_folder"):

            temp_comics_links = pickle.load(open("comics_links_folder/" + file,"rb"))                

            comics_links.append(temp_comics_links)

        return(comics_links)
```

Really simple. We only load each file and put it in a single list. Now, for
the second function, that will go through each link extracting information:


```python

    def get_all_comics_info(comics_links, start_counter):

        all_comics_info = []

        for counter, link in enumerate(comics_links):        

            comic_info = get_comic_info_from_page(link)

            if comic_info:

                all_comics_info.append(comic_info)

            if (counter % 100 == 0 and counter != 0) or (counter + 

                start_counter == len(comics_links)-1):

                print(comic_info)

                pickle.dump(all_comics_info, open("Comics_info/comics_infos_" + "_" +

                            str((counter + start_counter) // 100) + ".p","wb"))

                pickle.dump(counter + start_counter, 

                            open("Comics_info/counter_comics_info.p","wb"))

                all_comics_info = []
```

Nothing we haven't seen yet. For the last function, we will join the
information of comics in a list of dictionaries and, finally, export this data
to a file:


```python

    def join_comics_info():

        comics_info = []

        # For each file in the "comics_info_files" folder

        for file in os.listdir("comics_info_files"):        

            # Join every file together, excluding the counter

            if file != "counter_comics_info.p":

                print(file)

                comic_info = pickle.load(open("comics_info_files/" + file,"rb"))

                for comic in comic_info:    

                    comics_info.append(comic)

        pickle.dump(comics_info, open("all_comics_info.p", "wb"))
```

Finally, we need to string all that we did together. For this, we basically
need to do one thing. We will use Python to check if each exported file
containing our links exist. If they exist and are complete, we will load it in
a variable. If not, we will run each function to extract the information. For
files that are divided into several other files, we will check if the folder
that contains the files exists and if it does not exist, we will create it as
well. Let's use the "os.path.isdir()" function to check for the folder and
"os.path.isfile()" to check for files. To check whether a scraping which is
divided into multiple files is completed, we will load the file that holds the
counter and check if it is equal to the amount of items in a given list. And
so we will close our scraping. Let's go to the code:


```python

    # Check if the file publisher_links.p exists; if it does not, do the scraping

    if os.path.isfile('publisher_links.p') == True:

        print("Publisher links file already exists... loading file")

        publisher_links = pickle.load(open("publisher_links.p","rb"))

    else:

        print("Publisher links file does not exists.")

        print("Scraping website for the publisher links")

        print("Links will be exported to publisher_links.p")

        publisher_links = get_publishers_links()

        pickle.dump(publisher_links, open("publisher_links.p","wb"))

        

    # Check if the series_links.p file exists; if it does not, do the scraping

    if os.path.isfile('series_links.p') == True:

        print("Series links file already exists... loading file")

        series_links = pickle.load(open("series_links.p","rb"))

    else:

        print("Series links file does not exists.")

        print("Scraping website for the series links")

        print("Links will be exported to series_links.p")

        series_links = get_series_links_from_publisher(publisher_links)

        pickle.dump(series_links, open("series_links.p","wb"))

     

    # Check if the comics_links_files folder exists

    if os.path.isdir('comics_links_files'):

        print("Folder comics_links_files already exists. Checking for files")

        # Check if the counter for the scraping code exists

        if os.path.isfile('comics_links_files/comics_links_counter.p'):

            # Load the counter and check if the scraping process is complete

            comics_links_counter = pickle.load(open("comics_links_files/comics_links_counter.p","rb"))       

            print("Current count: " + str(comics_links_counter+1) + " of " +

                  str(len(series_links)))

            # If the scraping is complete, load the files

            if comics_links_counter + 1 == len(series_links):

                print("Scraping already completed. Loading files")

                comics_links = join_comics_links()

            # If the scraping is not complete, continue it

            else:

                print("Scraping initiated but not completed. Continuing...")

                get_issues_links_from_series(series_links[comics_links_counter+1:], 

                                             comics_links_counter)

                comics_links = join_comics_links()            

        # If the file does not exists, start the scraping

        else:

            print("Comics links extracting not started.")

            print("Starting comics links scraping")

            comics_links_counter = 0

            get_issues_links_from_series(series_links, comics_links_counter)

            comics_links = join_comics_links()

    # If the folder does not exist, create the folder and start the scraping

    else:

        print("Folder does not exists.")

        print("Creating folder comics_links_files")

        os.makedirs("comics_links_files")

        print("Starting comics links scraping")

        comics_links_counter = 0

        get_issues_links_from_series(series_links, comics_links_counter)

        comics_links = join_comics_links()

    

    # Check if the comics_info_files folder exists    

    if os.path.isdir('comics_info_files'):

        print("Folder comics_info_files already exists. Checking for files")

        # Check if the counter file exists

        if os.path.isfile('comics_info_files/comics_info_counter.p'):

            # If it exists, load the counter

            comics_info_counter = pickle.load(open("comics_info_counter.p"))        

            print("Current count: " + str(comics_info_counter+1) + " of " +

                  str(len(comics_links)))

            # If the counter is equal to the quantity of items in the comics_links list

            if comics_info_counter + 1 == len(comics_links):

                print("Scraping already completed. Loading files")            

            # If the counter is not equal to the quantity of items, continue 

    		# the scraping

            else:

                print("Scraping initiated but not completed. Continuing...")

                get_all_comics_info(comics_links[comics_info_counter+1:], 

                                    comics_info_counter)

        # If the counter does not exists, start the scraping

        else:

            print("Comics info extracting not started.")

            print("Starting comics info scraping")

            comics_info_counter = 0

            get_all_comics_info(comics_links, comics_info_counter)

    # If the folder does not exists, create it and start the scraping

    else:

        print("Folder does not exists.")

        print("Creating folder comics_info_files")

        os.makedirs("comics_info_files")

        print("Starting comics info scraping")

        comics_info_counter = 0

        get_all_comics_info(comics_links, comics_info_counter)

    

    # Load information from comics and create a DataFrame with it

    comics_info = pickle.load(open("comics_info_files/all_comics_info.p","rb"))

    comics_df = pd.DataFrame(comics_info)
```

And just like that, we finish our scraping. In the next post, I will make an
analysis of the data in order to understand a little better the world of
digital comics and come to some interesting conclusions.

PS: Suggestions and corrections to improve the code are very welcome. If you
have one, feel free to send it in the comments.
