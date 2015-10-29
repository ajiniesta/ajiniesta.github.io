---
layout: post
title:  "MapReduce Quick Quickstart"
date:   2015-10-30 09:00:20
categories: java mapreduce hadoop quickquickstart
comments: true
---

Sometimes it's quite hard to start with a technology. Like with MapReduce. In some places you see that you need a cluster or pseudo distributed cluster, and that it's tedious to start with. How about a short introduction to the [MapReduce paradigm][mapreducewiki], and programing with the Hadoop Java API. We are going to show a very simple example different that is not the typical 'Wordcount'.

In this post I will show some code snapshots, for the detail code and the whole project, please check the [Github repository][repo].

The fastest way to test and to start getting the precise knowledge about this programming paradigm, is to execute the process in local. This way we don't need any external configuration. We just need the dependencies of the hadoop client, and we can execute this little application locally.

Dependencies

Let's review the dependencies needed for this example used in the Maven Pom file.

{% highlight xml linenos %}
<dependency>
	<groupId>org.apache.hadoop</groupId>
	<artifactId>hadoop-client</artifactId>
	<version>2.6.0</version>
</dependency>
{% endhighlight %}

MapReduce example

We are going to process a CSV file with the three fields, a date(in format yyyy/MM/dd), a concept and an amount. The goal of the example is to sum all the values. All the data are belonging to the same year.

In the map, we are just going to emit a pair with the date and the amount grouping by date.

{% highlight java linenos %}
protected void map(LongWritable key, Text value, Context context)
		throws IOException, InterruptedException {
	String[] splits = value.toString().split(",");
	context.write(new Text(splits[0]), new DoubleWritable(Double.parseDouble(splits[2])));
}
{% endhighlight %}

In the reducer we sum all the values group by the key, the date.

{% highlight java linenos %}
protected void reduce(Text key, Iterable<DoubleWritable> values, Context context) throws IOException,  InterruptedException {
	double acum = 0;
	for (DoubleWritable value : values) {
		acum += value.get();
	}
	context.write(key, new DoubleWritable(acum));
}
{%endhighlight%}

And to finish the source code for this example, we defined a Driver. We do the usual boilerplate about the definition of the mappers, reducers, internal key and value classes... As commented before, the job will be executed locally, we are going to take the dataset used by input from the classpath of the project that we are creating. And the output will be an output directory with a timestamp attached.

{%highlight java linenos%}
public class QuickDriver extends Configured implements Tool{

	private static void initLogs() {
		ConsoleAppender console = new ConsoleAppender();
		// configure the appender
		String PATTERN = "%d [%p|%c|%C{1}] %m%n";
		console.setLayout(new PatternLayout(PATTERN));
		console.setThreshold(Level.INFO);
		console.activateOptions();
		// add appender to any Logger (here is root)
		Logger.getRootLogger().addAppender(console);
	}

	public static void main(String[] args) throws Exception {
		initLogs();
		int exitCode = ToolRunner.run(new Configuration(), new QuickDriver(), args);
		System.exit(exitCode);
	}

	public int run(String[] args) throws Exception {
		Configuration conf = getConf();

		Job job = Job.getInstance(conf, "Quickstart MR");
		job.setJarByClass(QuickDriver.class);

		String input = getClass().getClassLoader().getResource("dataset").toString();
		FileInputFormat.addInputPath(job, new Path(input));
		String output = "output/out"+System.currentTimeMillis();
		FileOutputFormat.setOutputPath(job, new Path(output));

		job.setMapperClass(QuickMap.class);
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(DoubleWritable.class);

		job.setReducerClass(QuickReducer.class);

		job.setNumReduceTasks(1);

		boolean ok = job.waitForCompletion(true);

		return ok ? 0 : 1;
	}
}
{%endhighlight%}

Now we run the Driver class as a regular java application inside an IDE or through a Maven run. If we were submitting the job to a cluster, the logs will appear, but as long we execute them locally we have to add a ConsoleAppender to see the logs.

{% highlight bash %}
mvn exec:java -Dexec.mainClass="com.iniesta.blogsamples.quickmr.QuickDriver"
{% endhighlight%}

After we run an output directory will be created with 365 lines with the grouping and the result of the whole algorithm.

Hope you enjoy the simple code.

[mapreducewiki]:  https://en.wikipedia.org/wiki/MapReduce
[repo]: https://github.com/ajiniesta/blog-code-samples/tree/master/quickmr
