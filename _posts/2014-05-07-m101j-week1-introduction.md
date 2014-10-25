---
layout: post
title: "M101J: MongoDB for Java Developers. Week 1: Introduction."
date:   2014-05-07 20:23:30
tags: 10gen course curso db education freemarker java m101 m101j mongo mongodb nosql spark university week1
---

## INTRODUCTION

Following are presented the contents and answers to the HomeWorks belonging to *Week 1 (Introduction)* for "**M101J: MongoDB for Java Developers**" course offered by [https://university.mongodb.com/][1]{: target="_blank"} started at 2014-03-17.



## HOMEWORK 1.1
![HW 1.1 statement 1][hw11_Statement1]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}

![HW 1.1 statement 2][hw11_Statement2]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}

### ANSWER HOMEWORK 1.1:
Simply it was about verifying the correct MongoDB installation, example data import and basic query execution over those imported data.
First, `mongorestore`:

![HW 1.1 mongorestore][hw11_MongoRestore]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}
{% highlight text linenos=table %}
$ l
dump/
$ /opt/mongodb-linux-i686-2.6.0/bin/mongorestore 
connected to: 127.0.0.1
2014-05-07T11:03:39.465-0500 dump/m101/hw1.bson
2014-05-07T11:03:39.466-0500    going into namespace [m101.hw1]
1 objects found
2014-05-07T11:03:39.514-0500    Creating index: { key: { _id: 1 }, ns: "m101.hw1", name: "_id_" }
2014-05-07T11:03:39.595-0500 dump/m101/funnynumbers.bson
2014-05-07T11:03:39.595-0500    going into namespace [m101.funnynumbers]
100 objects found
2014-05-07T11:03:39.598-0500    Creating index: { key: { _id: 1 }, ns: "m101.funnynumbers", name: "_id_" }
$
{% endhighlight %}
Then, some MongoShell checks about the restoration and the result answer:

![HW 1.1 mongo checks][hw11_MongoChecks]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}
{% highlight text linenos=table %}
> show dbs
admin    (empty)
blog     0.078GB
enron    0.953GB
final7   0.078GB
local    0.078GB
school   0.078GB
test     0.078GB
weather  0.078GB
> show dbs
admin    (empty)
blog     0.078GB
enron    0.953GB
final7   0.078GB
local    0.078GB
m101     0.078GB
school   0.078GB
test     0.078GB
weather  0.078GB
> use m101
switched to db m101
> show tables
funnynumbers
hw1
system.indexes
> db.hw1.count()
1
> db.hw1.findOne()
{
    "_id" : ObjectId("50773061bf44c220307d8514"),
    "answer" : 42,
    "question" : "The Ultimate Question of Life, The Universe and Everything"
}
>
{% endhighlight %}



## HOMEWORK 1.2
![HW 1.2 statement][hw12_Statement]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}

### ANSWER HOMEWORK 1.2:
Simple theoretic question answered having the notion of JSON structure and syntax, see [http://www.json.org/](http://www.json.org/) .

* WRONG. The separator between *key:value* pairs is a `,` (comma), NOT a `;` (semi-colon).
* CORRECT. It's a valid JSON document, maybe one thing to note is that the value of the *quotes* key is an array, and still a valid document.
* CORRECT. The empty document IS a valid document.
* WRONG. A couple of errors and doubt interpretations here: the value of the *boros* key IS NOT valid, it's not a document nor an array, to be a valid document must have *key:value* pairs separated by `,` (commas) inside enclosing `{` and `}` (curly braces) and it doesn't, and to be a valid array must be a list of elements separated by `,` (commas) inside enclosing `[` and `]` (square brackets) and it doesn't; further, the *"population"* and *7999034* items ARE NOT key NOR value NOR array elements of anything; so this "document" is all broke and wrong.
* CORRECT. This is a valid and fairly common nested document mixing both objects and arrays.



## HOMEWORK 1.3
![HW 1.3 statement 1][hw13_Statement1]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}

![HW 1.3 statement 2][hw13_Statement2]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}

### ANSWER HOMEWORK 1.3:
Like *HW 1.1*, it was about verifying the correct MongoDB installation and example data import, furthermore installation and correctness of JDK, Maven, mongo java driver and others.

Whole Maven project was provided by course, with his `pom.xml` file, packages, classes and others; here is shown the main file `Week1Homework3.java`:
{% highlight java linenos=table %}
/*
 * Copyright (c) 2008 - 2013 10gen, Inc. <http://10gen.com>
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 * 
 *   http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 *
 */

package com.tengen;

import com.mongodb.AggregationOutput;
import com.mongodb.BasicDBObject;
import com.mongodb.DB;
import com.mongodb.DBCollection;
import com.mongodb.DBObject;
import com.mongodb.MongoClient;

import java.net.UnknownHostException;

public class Week1Homework3 {
    public static void main(String[] args) throws UnknownHostException {
        MongoClient client = new MongoClient();

        DB database = client.getDB("m101");
        DBCollection collection = database.getCollection("funnynumbers");

        // Not necessary yet to understand this.  It's just to prove that you
        // are able to run a command on a mongod server
        AggregationOutput output =
                collection.aggregate(
                        new BasicDBObject("$group",
                                new BasicDBObject("_id", "$value")
                                        .append("count", new BasicDBObject("$sum", 1)))
                        ,
                        new BasicDBObject("$match", new BasicDBObject("count",
                                new BasicDBObject("$gt", 2))),
                        new BasicDBObject("$sort", new BasicDBObject("_id", 1))
                );

        int answer = 0;
        for (DBObject doc : output.results()) {
            answer += (Double) doc.get("_id");
        }

        System.out.println("THE ANSWER IS: " + answer);
    }
}
{% endhighlight %}
Here the compilation and execution of the program, and the answer result obtained:

![HW 1.3 terminal 1][hw13_Terminal1]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}

![HW 1.3 terminal 2][hw13_Terminal2]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}
{% highlight text linenos=table %}
$ l
pom.xml  src/  target/
$ mvn compile exec:java -Dexec.mainClass=com.tengen.Week1Homework3
[INFO] Scanning for projects...
[INFO] Searching repository for plugin with prefix: 'exec'.
[INFO] ------------------------------------------------------------------------
[INFO] Building M101J-week1-homework3
[INFO]    task-segment: [compile, exec:java]
[INFO] ------------------------------------------------------------------------
[INFO] [resources:resources {execution: default-resources}]
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /src/main/resources
[INFO] [compiler:compile {execution: default-compile}]
[INFO] Nothing to compile - all classes are up to date
[INFO] [exec:java {execution: default-cli}]
[WARNING] Warning: killAfter is now deprecated. Do you need it ? Please comment on MEXEC-6.
THE ANSWER IS: 366
[WARNING] thread Thread[MongoCleaner12877540,5,com.tengen.Week1Homework3] was interrupted but is still alive after waiting at least 15000msecs
[WARNING] thread Thread[MongoCleaner12877540,5,com.tengen.Week1Homework3] will linger despite being asked to die via interruption
[WARNING] NOTE: 1 thread(s) did not finish despite being asked to  via interruption. This is not a problem with exec:java, it is a problem with the running code. Although not serious, it should be remedied.
[WARNING] Couldn't destroy threadgroup org.codehaus.mojo.exec.ExecJavaMojo$IsolatedThreadGroup[name=com.tengen.Week1Homework3,maxpri=10]
java.lang.IllegalThreadStateException
    at java.lang.ThreadGroup.destroy(ThreadGroup.java:775)
    at org.codehaus.mojo.exec.ExecJavaMojo.execute(ExecJavaMojo.java:328)
    at org.apache.maven.plugin.DefaultPluginManager.executeMojo(DefaultPluginManager.java:490)
    at org.apache.maven.lifecycle.DefaultLifecycleExecutor.executeGoals(DefaultLifecycleExecutor.java:694)
    at org.apache.maven.lifecycle.DefaultLifecycleExecutor.executeStandaloneGoal(DefaultLifecycleExecutor.java:569)
    at org.apache.maven.lifecycle.DefaultLifecycleExecutor.executeGoal(DefaultLifecycleExecutor.java:539)
    at org.apache.maven.lifecycle.DefaultLifecycleExecutor.executeGoalAndHandleFailures(DefaultLifecycleExecutor.java:387)
    at org.apache.maven.lifecycle.DefaultLifecycleExecutor.executeTaskSegments(DefaultLifecycleExecutor.java:348)
    at org.apache.maven.lifecycle.DefaultLifecycleExecutor.execute(DefaultLifecycleExecutor.java:180)
    at org.apache.maven.DefaultMaven.doExecute(DefaultMaven.java:328)
    at org.apache.maven.DefaultMaven.execute(DefaultMaven.java:138)
    at org.apache.maven.cli.MavenCli.main(MavenCli.java:362)
    at org.apache.maven.cli.compat.CompatibleMain.main(CompatibleMain.java:60)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:606)
    at org.codehaus.classworlds.Launcher.launchEnhanced(Launcher.java:315)
    at org.codehaus.classworlds.Launcher.launch(Launcher.java:255)
    at org.codehaus.classworlds.Launcher.mainWithExitCode(Launcher.java:430)
    at org.codehaus.classworlds.Launcher.main(Launcher.java:375)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 24 seconds
[INFO] Finished at: Wed May 07 11:27:35 COT 2014
[INFO] Final Memory: 17M/41M
[INFO] ------------------------------------------------------------------------
$
{% endhighlight %}



## HOMEWORK 1.4
![HW 1.4 statement 1][hw14_Statement1]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}

![HW 1.4 statement 2][hw14_Statement2]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}

### ANSWER HOMEWORK 1.4:
Like *HW's 1.1 and 1.3*, it was about verifying the correctness of MongoDB installation, example data import, JDK, Maven, mongo java driver, furthermore Spark, Freemarker and others.

Whole Maven project was provided by course, with his `pom.xml` file, packages, classes and others; here is shown the main file `Week1Homework4.java`:
{% highlight java linenos=table %}
/*
 * Copyright (c) 2008 - 2013 10gen, Inc. <http://10gen.com>
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 * 
 *   http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 *
 */

package com.tengen;

import com.mongodb.AggregationOutput;
import com.mongodb.BasicDBObject;
import com.mongodb.DB;
import com.mongodb.DBCollection;
import com.mongodb.DBObject;
import com.mongodb.MongoClient;
import com.mongodb.ServerAddress;
import freemarker.template.Configuration;
import freemarker.template.Template;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import spark.Request;
import spark.Response;
import spark.Route;
import spark.Spark;

import java.io.StringWriter;
import java.net.UnknownHostException;
import java.util.HashMap;
import java.util.Map;

public class Week1Homework4 {
    private static final Logger logger = LoggerFactory.getLogger("logger");

    public static void main(String[] args) throws UnknownHostException {
        final Configuration configuration = new Configuration();
        configuration.setClassForTemplateLoading(
                Week1Homework4.class, "/");

        MongoClient client = new MongoClient(new ServerAddress("localhost", 27017));

        DB database = client.getDB("m101");
        final DBCollection collection = database.getCollection("funnynumbers");

        Spark.get(new Route("/") {
            @Override
            public Object handle(final Request request,
                                 final Response response) {
                StringWriter writer = new StringWriter();
                try {
                    Template helloTemplate = configuration.getTemplate("answer.ftl");

                    // Not necessary yet to understand this.  It's just to prove that you
                    // are able to run a command on a mongod server
                    AggregationOutput output =
                            collection.aggregate(
                                    new BasicDBObject("$group",
                                            new BasicDBObject("_id", "$value")
                                                    .append("count", new BasicDBObject("$sum", 1)))
                                    ,
                                    new BasicDBObject("$match", new BasicDBObject("count",
                                            new BasicDBObject("$lte", 2))),
                                    new BasicDBObject("$sort", new BasicDBObject("_id", 1))
                            );

                    int answer = 0;
                    for (DBObject doc : output.results()) {
                        answer += (Double) doc.get("_id");
                    }

                    Map<String, String> answerMap = new HashMap<String, String>();
                    answerMap.put("answer", Integer.toString(answer));

                    helloTemplate.process(answerMap, writer);
                } catch (Exception e) {
                    logger.error("Failed", e);
                    halt(500);
                }
                return writer;
            }
        });
    }
}
{% endhighlight %}
Here the compilation and execution of the program launching the Spark web application:

![HW 1.4 terminal][hw14_Terminal]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}
{% highlight text linenos=table %}
$ l
pom.xml  src/  target/
$ mvn compile exec:java -Dexec.mainClass=com.tengen.Week1Homework4
[INFO] Scanning for projects...
[INFO] Searching repository for plugin with prefix: 'exec'.
[INFO] ------------------------------------------------------------------------
[INFO] Building M101J
[INFO]    task-segment: [compile, exec:java]
[INFO] ------------------------------------------------------------------------
[INFO] [resources:resources {execution: default-resources}]
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO] snapshot spark:spark:0.9.9.4-SNAPSHOT: checking for updates from Spark repository
Downloading: http://www.sparkjava.com/nexus/content/repositories/spark//org/slf4j/slf4j-simple/1.6.4/slf4j-simple-1.6.4.pom
[INFO] Unable to find resource 'org.slf4j:slf4j-simple:pom:1.6.4' in repository Spark repository (http://www.sparkjava.com/nexus/content/repositories/spark/)
Downloading: http://repo1.maven.org/maven2/org/slf4j/slf4j-simple/1.6.4/slf4j-simple-1.6.4.pom
1K downloaded  (slf4j-simple-1.6.4.pom)
Downloading: http://www.sparkjava.com/nexus/content/repositories/spark//org/slf4j/slf4j-simple/1.6.4/slf4j-simple-1.6.4.jar
[INFO] Unable to find resource 'org.slf4j:slf4j-simple:jar:1.6.4' in repository Spark repository (http://www.sparkjava.com/nexus/content/repositories/spark/)
Downloading: http://repo1.maven.org/maven2/org/slf4j/slf4j-simple/1.6.4/slf4j-simple-1.6.4.jar
7K downloaded  (slf4j-simple-1.6.4.jar)
[INFO] [compiler:compile {execution: default-compile}]
[INFO] Nothing to compile - all classes are up to date
[INFO] [exec:java {execution: default-cli}]
[WARNING] Warning: killAfter is now deprecated. Do you need it ? Please comment on MEXEC-6.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
636 [Thread-2] INFO org.eclipse.jetty.util.log - jetty-7.3.0.v20110203
710 [Thread-2] INFO org.eclipse.jetty.util.log - Started SocketConnector@0.0.0.0:4567
{% endhighlight %}
Finally, the result obtained browsing to the recently launched Spark web application:

![HW 1.4 browser][hw14_Browser]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}



## PROGRESS
![Progress week 1][progress]{: width="75%" style="display:block; margin-left:auto; margin-right:auto"}



## REFERENCES
* [https://university.mongodb.com/][1]{: target="_blank"}
* [http://api.mongodb.org/][2]{: target="_blank"}
* [http://www.mongodb.com/][3]{: target="_blank"}
* [http://hao-deng.blogspot.com/search/label/Mongodb][4]{: target="_blank"}
* [http://ankurthetechie.blogspot.com/2013/09/final-exam-answers-with-explanation-for.html][5]{: target="_blank"}
* [http://techinote.com/mongodb-final-exam/][6]{: target="_blank"}

[1]: https://university.mongodb.com/
[2]: http://api.mongodb.org/
[3]: http://www.mongodb.com/
[4]: http://hao-deng.blogspot.com/search/label/Mongodb
[5]: http://ankurthetechie.blogspot.com/2013/09/final-exam-answers-with-explanation-for.html
[6]: http://techinote.com/mongodb-final-exam/

[hw11_Statement1]: {{ site.baseurl }}/assets/hw11_Statement1.png
[hw11_Statement2]: {{ site.baseurl }}/assets/hw11_Statement2.png
[hw12_Statement]: {{ site.baseurl }}/assets/hw12_Statement.png
[hw13_Statement1]: {{ site.baseurl }}/assets/hw13_Statement1.png
[hw13_Statement2]: {{ site.baseurl }}/assets/hw13_Statement2.png
[hw14_Statement1]: {{ site.baseurl }}/assets/hw14_Statement1.png
[hw14_Statement2]: {{ site.baseurl }}/assets/hw14_Statement2.png
[progress]: {{ site.baseurl }}/assets/progress.png

[hw11_MongoRestore]: {{ site.baseurl }}/assets/hw11_MongoRestore.png
[hw11_MongoChecks]: {{ site.baseurl }}/assets/hw11_MongoChecks.png
[hw13_Terminal1]: {{ site.baseurl }}/assets/hw13_Terminal1.png
[hw13_Terminal2]: {{ site.baseurl }}/assets/hw13_Terminal2.png
[hw14_Terminal]: {{ site.baseurl }}/assets/hw14_Terminal.png
[hw14_Browser]: {{ site.baseurl }}/assets/hw14_Browser.png
