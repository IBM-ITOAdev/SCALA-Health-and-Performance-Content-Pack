# IBM Smart Cloud Analytics Log Analysis (SCALA) Health and Performance Content Pack

# Description

This is a cookbook for creating your own content packs for managing the health and performance of your SCALA deployment.  Within IBM, we have referred to this in other products as being able to "manage our stuff with our stuff" or MOSWOS.  This repository provides you with the ability to use logstash to parse most of the SCALA log files and stream them into custom DSV Content Packs for search and visualization within your SCALA instance.

# What's Included?

1. A sample logstash configuration **(scala-health-performance-logstash.conf)** to parse and annotate the following SCALA v1.2.0.3 log files: 

	- type => "SCALA-GR" 	: GenericReceiver.log
	- type => "SCALA-UA" : UnityApplication.log
	- type => "SCALA-EIFR" : UnityEIFReceiver.log
	- type => "SCALA-SOLR" : solr.log
	- type => "SCALA-MANAGE-SOLR-NODES" : ManageSolrnodes.log
	- type => "SCALA-ZOOKEEPER" : zookeeper.out

2. A logstash grok pattern file for parsing the above SCALA v1.2.0.3 log files. **(SCALAPATTERNS)**

3. Custom DSV Content Pack properties files to create CSV based SCALA source types for the following:

	- genericReceiver.props
	- genericReceiverStats.props
	- unityApplication.props
	- ua-stats.props
	- unityEIFReceiver.props
	- eifr-stats.props
	- solr.props
	- solrManageNodes.props
	- zookeeper.props

4. Sample SCALA Dashboard Apps (coming soon)

# Deploying the Content Pack

1. Determine how you will ship the SCALA logs to logstash. There are many options including using rsyslog/syslog-ng, logstash-forwarder, LFA or the logstash file input if you plan to run logstash on the SCALA server or manually copy logs over to your logstash server for bulk processing.  

2. Review the sample logstash configuration **scala-health-performance-logstash.conf** and determine how you will use the inputs, filters and outputs.  You can use this example or just copy the needed parts into your main logstash configuration file. Pay close attention to the NOTES within the **scala-health-performance-logstash.conf** 

3. Review the filters section and determine if any changes are needed based upon how you use logstash, which version you use, etc. 

4. Place the logstash GROK pattern file **SCALAPATTERNS** into the appropriate location.  If you are using logstash 1.4.2, you may place this into the /patterns directory and you'll be all set. If you are using logstash 1.5 beta, you can place it into the same /patterns directory, but you will need to use the patterns_dir setting to this location in each filter using the GROK patterns (eg multiline, grok).

5. Review the output section and update based on your current logstash integration with SCALA. This example configuration will only work with the upcoming release of the SCALA-Logstash Integration.  If you do not have this, contact Doug McClure for assistance.

6. Copy the DSV Pack properties files you plan on using into the $SCALAHOME/unity_content/DSVToolkit_v1.1.0.2 directory.

7. Edit each DSV Pack properties file and update the scalaHome directory path.  You may also want to optimize the various index configuration fields in this file. For example, setting filterable = false for the processID field if you don't feel faceting on processID would be useful.

8. From the $SCALAHOME/unity_content/DSVToolkit_v1.1.0.2 directory, execute this command to build and deploy each DSV Content Pack you plan on using into SCALA: **python dsvGen.py [FILENAME].props -d -f -u unityadmin -p unityadmin**

9. Create SCALA datasources which use the new DSV Content Pack source types that you have deployed for each SCALA log type. Be sure to set the host and path values to match what you're setting in your logstash configuration. 

10. Start up logstash and verify logs are being consumed properly. Watch for activity in logstash stdout or within SCALA search results.

# Need Help?

This is a community contributed content pack and no explicit support, guarantee or warranties are provided by IBM nor the contributor. Feel free to engage the community on the ITOAdev forum if you need help!