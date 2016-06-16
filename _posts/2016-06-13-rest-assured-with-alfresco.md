---
layout: post
title: REST Assured with Alfresco
subtitle: Integration testing of REST web scripts
bigimg: /img/path.jpg
---

Here's a suggestion for integration testing REST webscripts using the 
[REST-assured Java DSL for easy testing of REST services](https://github.com/rest-assured/rest-assured)

{% highlight java %}
```
package com.bettercode.alfresco.webservices.integration;

/**
 * Created by martian on 13/06/16.
 */

import static org.junit.Assert.*;

import java.io.IOException;
import java.net.MalformedURLException;
import java.net.URI;
import java.net.URISyntaxException;
import java.util.Map;

import com.jayway.restassured.response.Response;
import org.apache.http.client.HttpClient;
import org.eclipse.jetty.util.ajax.JSON;
import org.json.JSONException;
import org.json.JSONObject;
import org.json.JSONTokener;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import static com.jayway.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class TestHofDownload {

    private static final String ADMIN_USER = "admin";
    private static final String ADMIN_PASS = "admin";

    private static final String LOCAL_URL = "http://localhost:8080/alfresco/s/sam";
    private HttpClient client;
    private URI url;


    @Before
    public void setUp() throws Exception {
        System.out.println("===========================");
        url = new URI(LOCAL_URL + "/demoProductService");

    }

    @After
    public void tearDown() {
        System.out.println("**********");

    }

    @Test
    public void obviouslyFailingTest() throws Exception {
        System.out.println("obviouslyFailingTest()");
        given().auth().preemptive().basic(ADMIN_USER, ADMIN_PASS).when().get(url).then().body("ping", equalTo("ok"));

    }

    @Test
    public void emptyCallGivesEmptyProductsInBody() throws Exception {

        /*
            We get under the hood of rest assured a little bit here
            
            Normally would have been, for example:

            given().auth().preemptive().basic(ADMIN_USER, ADMIN_PASS).when().get(url).then().body("ping", equalTo("ok"));
 
            like in the obviouslyFailingTest above.


            except here instead we get a response and use its asString() value to create the json object, you can also
            do string assertions here for negative matching: http://stackoverflow.com/a/26514466/370191

            The expected response for this call is this or equivalent: {"products" : []}


         */
        Response r = given().auth().preemptive().basic(ADMIN_USER, ADMIN_PASS).when().get(url);
        System.out.println(r.asString());

        JSONTokener tokr = new JSONTokener(r.asString());

        JSONObject root = new JSONObject(tokr);

        assertEquals("products array is empty on empty request", 0, root.getJSONArray("products").length());
        assertNotEquals("products array is empty on empty request, and not by accident", 1, root.getJSONArray("products").length());
        assertNotEquals("products array is empty on empty request, and not by accident", 10, root.getJSONArray("products").length());

    }


    @Test
    public void testThatDoesNotTestJustLogs() throws Exception {
        System.out.println("testThatDoesNotTestJustLogs()");

        System.out.println(url);
        System.out.println("body:");
        given().auth().preemptive().basic(ADMIN_USER, ADMIN_PASS).when().get(url).then().log().body();
        System.out.println("status:");
        given().auth().preemptive().basic(ADMIN_USER, ADMIN_PASS).when().get(url).then().log().status(); // Only log the status line
        System.out.println("response headers:");
        given().auth().preemptive().basic(ADMIN_USER, ADMIN_PASS).when().get(url).then().log().headers();  // Only log the response headers
        System.out.println("response cookies:");
        given().auth().preemptive().basic(ADMIN_USER, ADMIN_PASS).when().get(url).then().log().cookies();  // Only log the response cookies
    }

}
```
{% endhighlight %}
