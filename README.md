# SignUp
This project is about practicing how to create servlets and connect each web page with servlets. We have 3 pages are login page, a registration page, and an index page(which shows when u can log in with the correct username and password). The steps below are the brief guides.
## Step1 : Set the welcome page.
go to the deployment descriptor which is named web.xml and change the implemented code as follows. this will make index.jsp the first page (if users have logged in)
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd" id="WebApp_ID" version="4.0">
  <display-name>signup</display-name>
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
 
  </welcome-file-list>
</web-app>
```
