# Narrative-specific instructions

```
mvn install
awsume sudo -a
version="2.0.3-spark-3.3-narrative0"
aws s3 cp ~/.m2/repository/com/amazon/deequ/deequ/${version}/deequ-${version}.pom s3://narrative-artifact-releases/com/amazon/deequ/deequ/${version}/deequ-${version}.pom
aws s3 cp ~/.m2/repository/com/amazon/deequ/deequ/${version}/deequ-${version}.jar s3://narrative-artifact-releases/com/amazon/deequ/deequ/${version}/deequ-${version}.jar
aws s3 cp ~/.m2/repository/com/amazon/deequ/deequ/${version}/deequ-${version}-tests.jar s3://narrative-artifact-releases/com/amazon/deequ/deequ/${version}/deequ-${version}-tests.jar

```


Options to fully automate:
- https://github.com/ehsaniara/maven-repository-aws-s3
- https://jmchung.github.io/post/using-amazon-s3-as-a-private-maven-repository/
