---
layout: post
title: 在swift里完成网络请求
description: 在swift里，发出网络请求（http）,有很多方式可以用-NSURL,NSURLRequest,NSURLSession。IOS7之后，NSURLSession是执行网络请求的优先选择
categories: ios
---

这里，我们使用NSURLSession从一个假定的网站下载数据，可以用Xcode的playground很轻松的完成.

例如：

```
func searchItunesFor(searchTerm: String) {
     
    // The iTunes API wants multiple terms separated by + symbols, so replace spaces with + signs
    let itunesSearchTerm = searchTerm.stringByReplacingOccurrencesOfString(" ", withString: "+", options: NSStringCompareOptions.CaseInsensitiveSearch, range: nil)
     
    // Now escape anything else that isn't URL-friendly
    if let escapedSearchTerm = itunesSearchTerm.stringByAddingPercentEscapesUsingEncoding(NSUTF8StringEncoding) {
        let urlPath = "http://itunes.apple.com/search?term=\(escapedSearchTerm)&media=software"
        let url: NSURL = NSURL(string: urlPath)
        let session = NSURLSession.sharedSession()
        let task = session.dataTaskWithURL(url, completionHandler: {data, response, error -> Void in
            println("Task completed")
            if(error != nil) {
                // If there is an error in the web request, print it to the console
                println(error.localizedDescription)
            }
            var err: NSError?
             
            var jsonResult = NSJSONSerialization.JSONObjectWithData(data, options: NSJSONReadingOptions.MutableContainers, error: &err) as NSDictionary
            if(err != nil) {
                // If there is an error parsing JSON, print it to the console
                println("JSON Error \(err!.localizedDescription)")
            }
            let results: NSArray = jsonResult["results"] as NSArray
            dispatch_async(dispatch_get_main_queue(), {
                self.tableData = results
                self.appsTableView!.reloadData()
            })
        })
         
        task.resume()
    }
}
```
