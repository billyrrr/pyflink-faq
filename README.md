# pyflink-faq
Frequently Asked Questions about PyFlink

# Q1: Could not find any factory for identifier 'xxx' that implements 'org.apache.flink.table.factories.DynamicTableFactory' in the classpath.

Exception Stack:
```
py4j.protocol.Py4JJavaError: An error occurred while calling o13.execute.
: org.apache.flink.table.api.ValidationException: Unable to create a source for reading table 'default_catalog.default_database.sourceKafka'.

Table options are:

'connector'='kafka'
'format'='json'
'properties.bootstrap.servers'='192.168.101.109:9092'
'scan.startup.mode'='earliest-offset'
'topic'='pyflink_test'
	at org.apache.flink.table.factories.FactoryUtil.createTableSource(FactoryUtil.java:150)
	at org.apache.flink.table.planner.plan.schema.CatalogSourceTable.createDynamicTableSource(CatalogSourceTable.java:116)
	at org.apache.flink.table.planner.plan.schema.CatalogSourceTable.toRel(CatalogSourceTable.java:82)
	at org.apache.calcite.rel.core.RelFactories$TableScanFactoryImpl.createScan(RelFactories.java:495)
	at org.apache.calcite.tools.RelBuilder.scan(RelBuilder.java:1099)
	at org.apache.calcite.tools.RelBuilder.scan(RelBuilder.java:1123)
	at org.apache.flink.table.planner.plan.QueryOperationConverter$SingleRelVisitor.visit(QueryOperationConverter.java:351)
	at org.apache.flink.table.planner.plan.QueryOperationConverter$SingleRelVisitor.visit(QueryOperationConverter.java:154)
	at org.apache.flink.table.operations.CatalogQueryOperation.accept(CatalogQueryOperation.java:68)
	at org.apache.flink.table.planner.plan.QueryOperationConverter.defaultMethod(QueryOperationConverter.java:151)
	at org.apache.flink.table.planner.plan.QueryOperationConverter.defaultMethod(QueryOperationConverter.java:133)
	at org.apache.flink.table.operations.utils.QueryOperationDefaultVisitor.visit(QueryOperationDefaultVisitor.java:92)
	at org.apache.flink.table.operations.CatalogQueryOperation.accept(CatalogQueryOperation.java:68)
	at org.apache.flink.table.planner.plan.QueryOperationConverter.lambda$defaultMethod$0(QueryOperationConverter.java:150)
	at java.util.Collections$SingletonList.forEach(Collections.java:4824)
	at org.apache.flink.table.planner.plan.QueryOperationConverter.defaultMethod(QueryOperationConverter.java:150)
	at org.apache.flink.table.planner.plan.QueryOperationConverter.defaultMethod(QueryOperationConverter.java:133)
	at org.apache.flink.table.operations.utils.QueryOperationDefaultVisitor.visit(QueryOperationDefaultVisitor.java:47)
	at org.apache.flink.table.operations.ProjectQueryOperation.accept(ProjectQueryOperation.java:76)
	at org.apache.flink.table.planner.calcite.FlinkRelBuilder.queryOperation(FlinkRelBuilder.scala:184)
	at org.apache.flink.table.planner.delegation.PlannerBase.translateToRel(PlannerBase.scala:219)
	at org.apache.flink.table.planner.delegation.PlannerBase$$anonfun$1.apply(PlannerBase.scala:182)
	at org.apache.flink.table.planner.delegation.PlannerBase$$anonfun$1.apply(PlannerBase.scala:182)
	at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:234)
	at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:234)
	at scala.collection.Iterator$class.foreach(Iterator.scala:891)
	at scala.collection.AbstractIterator.foreach(Iterator.scala:1334)
	at scala.collection.IterableLike$class.foreach(IterableLike.scala:72)
	at scala.collection.AbstractIterable.foreach(Iterable.scala:54)
	at scala.collection.TraversableLike$class.map(TraversableLike.scala:234)
	at scala.collection.AbstractTraversable.map(Traversable.scala:104)
	at org.apache.flink.table.planner.delegation.PlannerBase.translate(PlannerBase.scala:182)
	at org.apache.flink.table.api.internal.TableEnvironmentImpl.translate(TableEnvironmentImpl.java:1665)
	at org.apache.flink.table.api.internal.TableEnvironmentImpl.translateAndClearBuffer(TableEnvironmentImpl.java:1657)
	at org.apache.flink.table.api.internal.TableEnvironmentImpl.execute(TableEnvironmentImpl.java:1607)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.apache.flink.api.python.shaded.py4j.reflection.MethodInvoker.invoke(MethodInvoker.java:244)
	at org.apache.flink.api.python.shaded.py4j.reflection.ReflectionEngine.invoke(ReflectionEngine.java:357)
	at org.apache.flink.api.python.shaded.py4j.Gateway.invoke(Gateway.java:282)
	at org.apache.flink.api.python.shaded.py4j.commands.AbstractCommand.invokeMethod(AbstractCommand.java:132)
	at org.apache.flink.api.python.shaded.py4j.commands.CallCommand.execute(CallCommand.java:79)
	at org.apache.flink.api.python.shaded.py4j.GatewayConnection.run(GatewayConnection.java:238)
	at java.lang.Thread.run(Thread.java:748)
Caused by: org.apache.flink.table.api.ValidationException: Cannot discover a connector using option: 'connector'='kafka'
	at org.apache.flink.table.factories.FactoryUtil.enrichNoMatchingConnectorError(FactoryUtil.java:587)
	at org.apache.flink.table.factories.FactoryUtil.getDynamicTableFactory(FactoryUtil.java:561)
	at org.apache.flink.table.factories.FactoryUtil.createTableSource(FactoryUtil.java:146)
	... 45 more
Caused by: org.apache.flink.table.api.ValidationException: Could not find any factory for identifier 'kafka' that implements 'org.apache.flink.table.factories.DynamicTableFactory' in the classpath.

Available factory identifiers are:

blackhole
datagen
filesystem
print
	at org.apache.flink.table.factories.FactoryUtil.discoverFactory(FactoryUtil.java:399)
	at org.apache.flink.table.factories.FactoryUtil.enrichNoMatchingConnectorError(FactoryUtil.java:583)
	... 47 more
```

It reuses the Java connectors implementations in PyFlink and most connectors are not bundled in the official PyFlink (and also Flink) distribution except the following connectors: blackhole, datagen, filesystem and print. So you need to specify the connector JAR package explicitly when executing PyFlink jobs:
- The connector JAR package could be found in the corresponding connector page in the official Flink documentation. For example, you can open the [Kafka connector page](https://nightlies.apache.org/flink/flink-docs-stable/docs/connectors/table/kafka/) and search keyword "SQL Client JAR" which is a fat JAR of Kafka connector.
- It should be noted that you should use the fat JAR which contains all the dependencies. Besides, the version of the connector JAR should be consistent with PyFlink version. 
- For how to specify the connector JAR in PyFlink jobs, you can refer to the [dependency management](https://nightlies.apache.org/flink/flink-docs-stable/docs/dev/python/dependency_management/#jar-dependencies) page of official PyFlink documentation.
