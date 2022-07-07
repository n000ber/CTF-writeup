# Burpsuite Academy (URL-based access control can be circumvented)

## Challenge: 

![challenge description](img/circumventing_url_based_para/chall%20desc.png)

## Solution:
This challenge requires us to use a custom-made request header `X-Original-URL` to bypass the URL-based access control to delete the user carlos.

Firstly, we visited the admin panel at `/admin`. As seen below, we were being blocked by the front-end system.

![blocked](img/circumventing_url_based_para/front%20end%20block.png)

Next, we used added the header `X-Original-URL: /admin` to the request.
> Do note that X-Original-URL is a custom-made header, ie for this lab's usage [only](https://security.stackexchange.com/questions/229928/x-original-url-and-x-rewrite-url-related-vulnerabilities). 

![adding X-original-url](img/circumventing_url_based_para/x%20original%20url.png)

Now, we were able to view the admin panel.

![viewing admin panel](img/circumventing_url_based_para/able%20to%20view%20the%20admin%20panel.png)

Clicking on delete for carlos' account, we were presented with the following screen.

![clicking on delete](img/circumventing_url_based_para/deleting%20user%20carlos.png)

From the URL, it is evident that we need to change the request header to `X-Original-URL: /admin/delete`, as well as providing the GET parameter containing the username of the user to be deleted.

![final payload](img/circumventing_url_based_para/final%20payloa.png)

Finally, we had completed the lab! :")

![completed lab](img/circumventing_url_based_para/solved%20lab.png)