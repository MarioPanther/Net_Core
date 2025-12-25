# Net_Core
Error solution of PUT Method and POST Method
In an ASP.NET Core 8 solution, a 405 Method Not Allowed error for PUT requests is typically caused by server-side restrictions or routing misconfigurations rather than client-side errors. 
1. IIS Hosting: WebDAV Module Interference 
If your application is hosted on Internet Information Services (IIS), the most common culprit is the WebDAVModule, which blocks PUT and DELETE verbs by default. 
Solution: Remove the module in your web.config file.

change into web.config only
<system.webServer>
  <modules>
    <remove name="WebDAVModule" />
  </modules>
  <handlers>
    <remove name="WebDAV" />
  </handlers>
</system.webServer>


if it was not done than alternative


Alternatively, you can disable WebDAV directly in the IIS Manager by navigating to Modules and removing WebDAVModule for your specific site. 
2. Controller Routing Issues
Routing mismatches in your code can cause the server to misinterpret the request. 
Missing Verb Attribute: Ensure your action is explicitly decorated with [HttpPut].
Missing Route Parameter: RESTful PUT methods often require an ID in the URL (e.g., api/controller/1). If your route template is [HttpPut("{id}")] but you call api/controller without an ID, the server may default to a matching GET or POST route and then reject the PUT verb.
Body Binding: Ensure parameters sent in the body are marked with [FromBody] to avoid binding failures that might trigger routing errors. 
3. CORS Policy Restrictions
If your frontend and backend are on different domains, your Cross-Origin Resource Sharing (CORS) policy must explicitly allow the PUT method. 
Solution: In Program.cs, verify your policy:


builder.Services.AddCors(options => {
    options.AddPolicy("AllowAll", policy => {
        policy.WithOrigins("http://your-frontend.com")
              .WithMethods("PUT", "POST", "GET", "DELETE") // Explicitly include PUT
              .AllowAnyHeader();
    });
});


Ensure app.UseCors("AllowAll") is placed before app.UseAuthorization() in the middleware pipeline. 
4. Other Server Configurations
Request Filtering: In IIS, check Request Filtering > Verbs. If specific verbs are listed, ensure PUT is allowed.
WAF/Firewalls: Security layers like Cloudflare or other Web Application Firewalls (WAF) might block PUT requests to prevent certain exploits. Check your firewall logs for blocked methods. 

