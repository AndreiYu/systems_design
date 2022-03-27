![web_crawler](https://puml-demo.herokuapp.com/github/AndreiYu/systems_design/blob/master/web_crawler/system-design.puml)

Web crawling process can be represented as graph traversing with Breadth-first search (BFS) approach since seed links points to nested links which
in their turn can either point to next links or to the parent.  
**URL frontier** is a key component here since it helps to overcome such issues as: 
* **politeness** (since most links from the same web page are linked back to the same host, when the crawler tries to download web pages in
  parallel, initial host will be flooded with requests)
* **priority** (we can prioritise which links to download based on page ranks, web traffic, update frequency, etc.) 
* **freshness** (web pages are constantly being added, deleted, and edited so we should re-crawl visited urls to check for update)

**Politeness**  
Generally, a web crawler should avoid sending too many requests to the same hosting server
within a short period. Sending too many requests is considered as “impolite” or even treated
as denial-of-service (DOS) attack. For example, without any constraint, the crawler can send
thousands of requests every second to the same website. This can overwhelm the web
servers.  
The general idea of enforcing politeness is to download one page at a time from the same
host. A delay can be added between two download tasks. The politeness constraint is
implemented by maintain a mapping from website hostnames to download (worker) threads.
Each downloader thread has a separate FIFO queue and only downloads URLs obtained from
that queue.  

**Priority**  
Prioritisation of urls can be based on page ranks, web traffic, update frequency, etc.  
Same approach as in 'politeness' case can be implemented:  
• Prioritizer takes URLs as input and computes the priorities.  
• Each queue has an assigned priority. Queues with high priority are selected with higher probability.  
• Queue selector: Randomly choose a queue with a bias towards queues with higher priority.  

**Freshness**  
Since it's resource-consuming to re-crawl all links, 2 strategies to re-crawl can be implemented:  
• Recrawl based on web pages’ update history.  
• Prioritize URLs and recrawl important pages first and more frequently.  

![url_frontier](https://puml-demo.herokuapp.com/github/AndreiYu/systems_design/blob/master/web_crawler/url-frontier.puml)

To guard against failures, crawl states and data can be written to
a storage system. A disrupted crawl can be restarted easily by loading saved states and
data.  

Other points to consider:
1. Redundant content
   Nearly 30% of the web pages are duplicates. Hashes or checksums help to detect duplication.  
2. Spider traps
   A spider trap is a web page that causes a crawler in an infinite loop. For instance, an infinite
   deep directory structure is listed as follows:  
   `www.spidertrapexample.com/foo/bar/foo/bar/foo/bar/…`  
   Such spider traps can be avoided by setting a maximal length for URLs. However, no onesize-
   fits-all solution exists to detect spider traps. Websites containing spider traps are easy to
   identify due to an unusually large number of web pages discovered on such websites. It is
   hard to develop automatic algorithms to avoid spider traps; however, a user can manually
   verify and identify a spider trap, and either exclude those websites from the crawler or apply
   some customized URL filters.  
3. Data noise  
   Some of the contents have little or no value, such as advertisements, code snippets, spam
   URLs, etc. Those contents are not useful for crawlers and should be excluded if possible.

### Reference materials
1) [Web Crawling](http://infolab.stanford.edu/~olston/publications/crawling_survey.pdf)
2) [Web Crawling 2](https://www.ics.uci.edu/~lopes/teaching/cs221W12/slides/Lecture05.pdf)
