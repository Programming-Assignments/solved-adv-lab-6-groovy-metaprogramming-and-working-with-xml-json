Download Link: https://assignmentchef.com/product/solved-adv-lab-6-groovy-metaprogramming-and-working-with-xml-json
<br>
In this lab you will explore some interesting capabilities of Groovy. You will use <u>runtime metaprogamming</u> to manipulate the properties and methods of an object. You will also use <u>builders</u> to create XML and JSON representations of an object, and create an object using data from a web API.

For this lab you will need to create and run Groovy scripts. You can do this using the Groovy command line tools or GroovyConsole, or you can use IntelliJ (recommended). If you are using IntelliJ, you should create a project lab6project.

Task 1.Metaprogramming

<strong>Creating an object to manipulate</strong>

<ol>

 <li>Create a new Groovy class, in your project’s src folder, called <em>Person</em>. Edit the class so that it contains the following code:</li>

</ol>

class Person {String nameString password

def resetPassword(String password) {this.password = password}}

<ol start="2">

 <li>Create a new Groovy script called <em>groovy</em>. In this task you will make some modifications to the Person class. Everything else you are asked to do will involve adding further lines of code to the <em>task1</em> script.</li>

 <li>Add code to the script to create an instance <em>p</em> of Person as follows:</li>

</ol>

def p = new Person()

and add code to set the values of the properties of <em>p</em> to <em>“Alice”</em> and <em>“aSecret”</em>. You will use this object throughout this task.

<ol start="4">

 <li>Add code to print the property values, and run your script.</li>

</ol>

<strong>Missing methods and properties</strong>

<ol>

 <li>Continue your script by adding the following code, which tries to access a method <em>someMethod</em></li>

</ol>

p.someMethod(3, “hello”)

Run the script. What happened? Review the method resolution order for Groovy in your lecture notes to see the steps that led to this outcome.

<ol start="2">

 <li>Add methods <em>methodMissing</em> and <em>invokeMethod</em> to the Person class. Each should print a message indicating the name of the method called, the arguments supplied, and which method was called in response. Run the script again. Which method was invoked? Is this what you would expect?</li>

</ol>




<ol start="3">

 <li>Remove or comment out the call to <em>someMethod</em> in your script. Add the following call (to a method that does exist):</li>

</ol>




p.resetPassword(“newpwd”)println(p.password)




The <em>println</em> statement will let you see whether the call to <em>resetPassword</em> worked. Run the script. Was the <em>resetPassword</em> method called?




<ol start="4">

 <li>Make the following modifications to your Person class:</li>

</ol>




<ul>

 <li>Make Person implement the interface GroovyInterceptable</li>

 <li>Use <em>out.println</em> instead of <em>println</em> in <em>invokeMethod<a href="#_ftn1" name="_ftnref1"><strong>[1]</strong></a></em></li>

</ul>




Run the script again. What happened? Was the <em>resetPassword</em> method called? Note that if a class implements GroovyInterceptable then an <em>invokeMethod</em> method will intercept <u>all</u> method calls




<ol start="5">

 <li>Add the following line to <em>invokeMethod</em> – this uses the object’s metaclass to find and invoke the method which was called:</li>

</ol>

metaClass.getMetaMethod(name, args).invoke(this, args)




Run the script again. What happened? Was the <em>resetPassword</em> method called?




Based on the results of the above steps, what do you think are the intended purposes of <em>invokeMethod</em> and <em>methodMissing</em> in Groovy?




<ol start="6">

 <li>Add a <em>propertyMissing</em> method to the Person class and add code to your script to test this.</li>

</ol>










<strong>Adding properties and methods at runtime</strong>




<ol>

 <li>Add the following to your script, immediately after the existing code:</li>

</ol>




p.metaClass.age = 21




This adds a property <em>age</em> to your Person object through its metaclass. Test that you can print the value of the property (add a print statement and run the script).




<ol start="2">

 <li>Add the following code to your script.</li>

</ol>




Scanner input = new Scanner(System.in)print(“new property name: “)while ((newPropName = input.next()) != “done”) {print(“new property value: “)newPropVal = input.next()p.metaClass.”${newPropName}” = newPropVal

print(“new property name: “)}

println(“Properties of object-“)for (prop in p.properties) {println(prop)}




This allows multiple properties to be added to the Person object at runtime with names and values input by the user, until the user enters <em>“done”</em> for the property name. The for loop iterates through and prints the properties of the object. Run the script and test that you can add properties to your Person object and see their names and values, along with those of the static properties.




<ol start="3">

 <li>You can also add methods at runtime as closures. Add the following code to your script to add a method <em>prettyPrint</em> to your person object which will list the properties and values in a tidier format:</li>

</ol>




p.metaClass.prettyPrint = {println “Person object has following properties”p.properties.each {if (it.key != ‘prettyPrint’)println ” ” + it.key + “: ” + it.value}}




Test that you can call the method <em>prettyPrint</em> on your Person object (add a call and run the script).




<ol start="4">

 <li>You can add methods dynamically to any type of object, not just instances of your own types. This includes instances of Java classes. Add code to your script to do the following:</li>

</ol>




<ul>

 <li>Create a new instance of the Java Date class called <em>d</em> (you can use the Date class in Groovy without any imports)</li>

 <li>Define a variable <em>clos1</em> and assign to it a closure containing the same code as used for <em>prettyPrint</em> above (except that the output shouldn’t refer to a “Person”)</li>

 <li>Assign the <em>clos1</em> to your Date object using:</li>

</ul>

d.metaClass.prettyPrint = clos1




Test that you can call the method <em>prettyPrint</em> on your Date object.







<strong>Closure – delegating to an object</strong>




<ol>

 <li>Add the following code to your script to create a closure and call that closure:</li>

</ol>




def clos2 = {resetPassword “aReallyBigSecret”}




clos()




<em>(Note that the code inside the closure is equivalent to </em>




resetPassword(“aReallyBigSecret”)

<em>as Groovy allows parentheses to be omitted here)</em>




What happened? Note that the closure has no access to a method or closure called <em>resetPassword</em>.




<ol start="2">

 <li>Assign your Person object to be the <u>delegate</u> for the closure using the following code <u>before</u> the call to clos:</li>

</ol>




clos.delegate = p




Add a call to <em>p.prettyPrint</em> so that you can see any change to the Person object’s properties. Run the script and check that the closure <em>clos2</em> has successfully changed the Person object’s password.




























<h3>Task 2. Building representations of an object</h3>




In this task you will create XML, HTML and JSON representations of a Person object, using the same Person class as in Task 1.




<ol>

 <li>Create a new Groovy script <em>groovy</em>. Download the file <em>task2.txt</em> from GCULearn and copy the code into your script. This code is essentially a part of the code for Task 1 which creates a Person object <em>p</em>, allows you to add some properties at runtime to it, and prints the property names and values. Run this script to make sure it works correctly.</li>

</ol>




Review the documentation for the Groovy MarkupBuilder class:




<a href="http://docs.groovy-lang.org/latest/html/api/groovy/xml/MarkupBuilder.html"><strong>http://docs.groovy-lang.org/latest/html/api/groovy/xml/MarkupBuilder.html</strong></a>




<ol start="2">

 <li>Add code to the script to do the following:</li>

</ol>




<ul>

 <li>Create an instance of StringWriter and assign it to a variable <em>writer</em></li>

 <li>Create an instance of MarkupBuilder, with writer as the single constructor parameter, and assign it to a variable <em>xml</em> (you will need to import groovy.xml.MarkupBuilder)</li>

</ul>




<ol start="3">

 <li>Use the following code to build and print an XML representation of your Person object:</li>

</ol>




xml.person() {name(p.name)password(p.password)dynamicproperties {for (prop in p.properties) {if (![‘class’,’name’,’password’].contains(prop.key))“${prop.key}”(“${prop.value}”)}}}println writer




What do you think the purpose of the if statement is?




Run the script, adding a single property names <em>email</em>, at runtime. You should get a representation of your object similar to the following – note how the code and properties correspond to the XML.




&lt;person&gt;

&lt;name&gt;Alice&lt;/name&gt;

&lt;password&gt;aSecret&lt;/password&gt;

&lt;dynamicproperties&gt;

&lt;age&gt;21&lt;/age&gt;

&lt;email&gt;<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="a8c9c4c1cbcde8cdd0c9c5d8c4cd86cbc7c5">[email protected]</a>&lt;/email&gt;

&lt;/dynamicproperties&gt;

&lt;/person&gt;

<strong> </strong>

Note that the call <em>xml.person()</em> produces an XML document with a root element called <em>person</em>. The code in {} after this is a closure. Each call in the closure, e.g <em>name(p.name)</em> produces an XML element containing the parameter value, e.g. &lt;name&gt;Alice&lt;/name&gt;. The <em>dynamicproperties</em> element has other elements nested inside it.




<ol start="4">

 <li>Add a similar block of code to create an HTML representation of the Person and run the script. You will need new StringWriter and MarkupBuilder objects, and should name the root element <em>html</em>. You should write calls inside the closure to produce HTML elements, e.g. <em>h1</em>. The result should look like this:</li>

</ol>




&lt;html&gt;

&lt;head&gt;

&lt;title&gt;Person object&lt;/title&gt;

&lt;/head&gt;

&lt;body&gt;

&lt;h1&gt;Name: Alice&lt;/h1&gt;

&lt;h3&gt;Password: aSecret&lt;/h3&gt;

&lt;h4&gt;Dynamic properties&lt;/h4&gt;

&lt;ul&gt;

&lt;li&gt;age: 21&lt;/li&gt;

&lt;li&gt;email: <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="60010c090305200518010d100c054e030f0d">[email protected]</a>&lt;/li&gt;

&lt;/ul&gt;

&lt;/body&gt;

&lt;/html&gt;




Create a new HTML file in your project called <em>person.html</em>, and copy the HTML from the console output into the file. Open the file in your browser (IntelliJ should give you an option to do this).




<ol start="5">

 <li>Now you will create a JSON (JavaScript Object Notation) representation of the Person object. Add the following code to your script. You will need to import groovy.json.JsonBuilder.</li>

</ol>




def json = new JsonBuilder()json.person() {for (prop in p.properties) {“${prop.key}”(“${prop.value}”)}}println json.toString()




This creates a simple JSON representation with all properties at the same level (no nesting). Run the script to see the JSON.




<ol start="6">

 <li>Finally, you can easily use JSON to create a Groovy object using a JsonSlurper object. Add the following code to your script to do this and print the properties of the object, which is actually a Map instance. You will need to import groovy.json.JsonSlurper.</li>

</ol>




def slurper = new JsonSlurper()def result = slurper.parseText(json.toString())

result.each { key, value -&gt;println “Object of type $key ”value.each { k, v -&gt;println “$k : $v”}}




Run the script and check that the properties (keys) have the same values as the original Person object.













<h3>Task 3. Getting data from a REST API</h3>




XML and JSON formats are widely used for making data available from a REST web API. A REST API allows programs to retrieve data by making HTTP requests. The URL of the request specifies what data the program wants to retrieve. Groovy makes it easy to work with REST APIs.




In this task you will use a “dummy” API which is available for testing and prototyping<a href="#_ftn2" name="_ftnref2">[2]</a>. While the data is not meaningful, this is a “live” service which is accessed over the internet.




<ol>

 <li>Enter the following URL in your browser address bar.</li>

</ol>




<a href="https://jsonplaceholder.typicode.com/photos/1"><strong>https://jsonplaceholder.typicode.com/photos/1</strong></a>




You should see the following data, in JSON format. This is the data you will consume from a Groovy script.




{

“albumId”: 1,

“id”: 1,

“title”: “accusamus beatae ad facilis cum similique qui sunt”,

“url”: “http://placehold.it/600/92c952”,

“thumbnailUrl”: “http://placehold.it/150/30ac17”

}




<ol start="2">

 <li>This data represents a photo. We want to use the data to create a Groovy object which is an instance of a class Photo with properties matching those in the JSON data. Create a new Groovy class Photo in the <em>src</em> folder in your project, with the properties <em>albumId</em>, <em>id</em> (both ints), <em>title</em>, <em>url</em> and <em>thumbnailUrl</em> (all Strings)</li>

</ol>




<ol start="3">

 <li>Create a new Groovy script <em>groovy</em> in your project. Add the following code to retrieve the data:</li>

</ol>




URL url =new URL(“https://jsonplaceholder.typicode.com/photos/4”)def data = url.getText(requestProperties: [‘User-Agent’: ‘Groovy Sample Script’])




<ol start="4">

 <li>Add the following code to parse the JSON data and use it to create a Groovy object, as you did in Task 2. This object is, as before, a Map instance.</li>

</ol>




def slurper = new JsonSlurper()def objectFromJson = slurper.parseText(data)

<ol start="5">

 <li>Groovy allows you to provide a Map as a constructor parameter for a Groovy class and it will match the map keys to the class properties and create an instance with the appropriate property values taken from the Map. Add the following code to do this and print the properties of the Photo object.</li>

</ol>




Photo ph = new Photo(objectFromJson)

ph.properties.each {println ” ” + it.key + “: ” + it.value}




Run the script and check that the properties match the data from the API. In the IntelliJ console you should be able to click on the <em>url</em> and <em>thumbnailUrl</em> property values and see the relevant photos (they’re not very exciting, actually) in your browser.

<a href="#_ftnref1" name="_ftn1">[1]</a> See http://stackoverflow.com/questions/30646899/groovy-println-vs-system-out-println-with-groovyinterceptable

<a href="#_ftnref2" name="_ftn2">[2]</a> https://jsonplaceholder.typicode.com/