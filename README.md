# SignUp
This project is about practicing how to create servlets and connect each web page with servlets. We have 3 pages are login page, a registration page, and an index page(which shows when u can log in with the correct username and password). The steps below are the brief guides.
## Brief.
![Architecture](https://github.com/caunhach/SignUp/blob/main/arch.png)<br>
<strong>important components: </strong><br>
- <strong>Server Container : Apache-Tomcat</strong> - A server container is a lightweight, standalone, and executable software package that includes everything needed to run a piece of software
- <strong>Deployment Descriptor : web.xml</strong> -  it is a deployment descriptor file used in Java web applications. It is an XML file that provides configuration information for the web components that comprise a web application. The web.xml file is typically located in the WEB-INF directory of a web application.
- <strong>Servlets : java files (login.java, logout.java, RegristrationServlet.java)</strong> - Servlets are Java classes that extend the capabilities of servers to handle requests and responses. They are part of the Java Platform, Enterprise Edition (Java EE), which is now known as Jakarta EE. Servlets are primarily used to create web applications, and they operate at the server-side to generate dynamic web content, process user input, and manage state.
- <strong>jsp files</strong> - JavaServer Pages (JSP) is a technology used in Java web development to create dynamic web pages. JSP allows developers to embed Java code in HTML pages, making it easier to build web applications with dynamic content.
- <strong>JDBC</strong> - Java Database Connectivity (JDBC) is a Java-based API that provides a standard interface for connecting and interacting with relational databases. JDBC allows Java applications to execute SQL queries, retrieve and update data in a database. It acts as a bridge between Java applications and database management systems (DBMS).
## Step1 : Set the welcome page.
go to the deployment descriptor which is named <strong>web.xml</strong> and change the implemented code as follows. this will make <strong>index.jsp</strong> the first page (if users have logged in)
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd" id="WebApp_ID" version="4.0">
  <display-name>signup</display-name>
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
 
  </welcome-file-list>
</web-app>
```
So, if the users are new or haven't logged in yet. we will send them to the login page with this code in index.jsp.
```
<%
	if(session.getAttribute("name")==null){
		response.sendRedirect("login.jsp");
	}
%>
```
## Step2 : Manage Registration Page.
In registration.jsp, set the method="post" and action="register". post is the method to request a web server accept and store the data enclosed in the body of the request message. and the value in action is the servelet where the data will be sent to.
```
<form method="post" action="register" class="register-form" id="register-form">
```
create a new servlet name RegistrationServlet.java and delete all except the doPost method. the code below, we set the local string to store value from a submitted form.
```
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		String uname = request.getParameter("name");
		String uemail = request.getParameter("email");
		String upwd = request.getParameter("pass");
		String umobile = request.getParameter("contact");
		RequestDispatcher dispatcher = null;
		Connection con = null;
```
use Connection object which is associated with database connectivity to communicate with mysql.
```
try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			con = DriverManager.getConnection("jdbc:mysql://localhost:3306/youtube", "root", "");
			PreparedStatement pst = con.prepareStatement("insert into users(uname, upwd, uemail, umobile) values(?,?,?,?) ");
			pst.setString(1, uname);
			pst.setString(2, upwd);
			pst.setString(3, uemail);
			pst.setString(4, umobile);
```
update database, if at least one row has been changed, set the status to be success.
```
int rowCount = pst.executeUpdate();
dispatcher = request.getRequestDispatcher("registration.jsp");
if (rowCount > 0) {
	request.setAttribute("status", "success");
				
} else {
	request.setAttribute("status", "failed");
}
dispatcher.forward(request, response);
```
back to the registration page, create a hidden button that will announce when registration success
```
<input type="hidden" id="status" value="<%= request.getAttribute("status") %>">
```
```
<script type="text/javascript">

	var status = document.getElementById("status").value;
	if(status == "success"){
		swal("Congrat", "Account Created Successfully", "success");
	}

</script>
```
## Step3 : Manage login Page.
like registration page, set the servlet destination where we redirect to.
```
<form method="post" action="Login" class="register-form"
id="login-form">
	<div class="form-group">
		<label for="username"><I
			class="zmdi zmdi-account material-icons-name"></i></label> <input
			type="text" name="username" id="username"
			placeholder="Your Name" />
	</div>
	<div class="form-group">
		<label for="password"><i class="zmdi zmdi-lock"></i></label> <input
			type="password" name="password" id="password"
			placeholder="Password" />
	</div>
	<div class="form-group">
		<input type="checkbox" name="remember-me" id="remember-me"
			class="agree-term" /> <label for="remember-me"
			class="label-agree-term"><span><span></span></span>Remember
			me</label>
	</div>
	<div class="form-group form-button">
		<input type="submit" name="signin" id="signing"
			class="form-submit" value="Log in" />
	</div>
</form>
```
create a new servlet name login.java and delete all except the doPost method. the code below, we set the local string to store value from a login form.
```
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
		String uemail =request.getParameter("username");
		String upwd =request.getParameter("password");
		HttpSession session = request.getSession();
		RequestDispatcher dispatcher = null;
```
connect MySQL, and execute a select query from a users table and insert the login data to it. store the result in rs. then find another row in rs(matching in the database). if yes go to index.jsp, otherwise go to login.jsp.
```
try {
			Class.forName("com.mysql.jdbc.Driver");
			Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/youtube?useSSL=false", "root", "");
			PreparedStatement pst = con.prepareStatement("select * from users where uemail = ? and upwd = ?");
			pst.setString(1, uemail);
			pst.setString(2, upwd);
			
			ResultSet rs = pst.executeQuery();
			if (rs.next()) {
				session.setAttribute("name", rs.getString("uname"));
				dispatcher = request.getRequestDispatcher("index.jsp");
			}else {
				request.setAttribute("status", "failed");
				dispatcher = request.getRequestDispatcher("login.jsp");
			}
			dispatcher.forward(request, response);
```
back to the login page, create a hidden button that will announce when login fails
```
<input type="hidden" id="status" value="<%= request.getAttribute("status") %>">
```
```
<script type="text/javascript">

	var status = document.getElementById("status").value;
	if(status == "failed"){
		swal("Sorry", "Wrong Username or Password", "error");
	}

</script>
```
## Step4 : Manage Logout Page.
buttons or links for logout in index.jsp
```
<li class="nav-item mx-0 mx-lg-1"><a
    class="nav-link py-3 px-0 px-lg-3 rounded" href="logout">Logout</a></li>
<li class="nav-item mx-0 mx-lg-1 bg-danger"><a
    class="nav-link py-3 px-0 px-lg-3 rounded" href="logout"><%=session.getAttribute("name") %></a></li>
```
terminate the session and send a redirect to login.jsp
```
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		HttpSession session = request.getSession();
		session.invalidate();
		response.sendRedirect("login.jsp");
	}
```
