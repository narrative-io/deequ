# Narrative-specific instructions

This is Narrative's fork of [awslabs/deequ](https://github.com/awslabs/deequ),
re-baselined on upstream `2.0.18` (Spark 3.5.7) and cross-built for **Scala 2.12 and 2.13**.

The published version is `<upstream-version>-narrative<n>` (e.g. `2.0.18-spark-3.5-narrative0`),
and artifacts are published under the Scala-suffixed coordinates `deequ_2.12` and `deequ_2.13`.

## Building

```
make build                      # Scala 2.12 (default profile)
mvn clean install -Pscala-2.12  # Scala 2.12 explicitly
mvn clean install -Pscala-2.13  # Scala 2.13
```

Use JDK 8 (see `.tool-versions`). Spark 3.5 also supports JDK 11/17.

## Publishing both Scala versions to the Narrative S3 artifact repo

```bash
version="2.0.18-spark-3.5-narrative0"
awsume sudo -a
for scala in 2.12 2.13; do
  mvn clean install -Pscala-${scala}
  base=~/.m2/repository/com/amazon/deequ/deequ_${scala}/${version}
  s3=s3://narrative-artifact-releases/com/amazon/deequ/deequ_${scala}/${version}
  aws s3 cp ${base}/deequ_${scala}-${version}.pom        ${s3}/deequ_${scala}-${version}.pom
  aws s3 cp ${base}/deequ_${scala}-${version}.jar        ${s3}/deequ_${scala}-${version}.jar
  aws s3 cp ${base}/deequ_${scala}-${version}-tests.jar  ${s3}/deequ_${scala}-${version}-tests.jar
done
```

Downstream consumers reference the Scala-suffixed coordinate, e.g.
`com.amazon.deequ:deequ_2.12:2.0.18-spark-3.5-narrative0` (or `deequ_2.13`).

## Updating to a newer upstream release

The narrative changes are a small set of commits layered directly on top of an upstream
release tag (the current base is `2.0.18`). To move to a newer upstream release, rebase
those commits onto the new tag — no manual file re-application:

```
git remote add upstream https://github.com/awslabs/deequ.git   # one-time
git fetch upstream --tags
git rebase --onto <new-upstream-tag> <current-base-tag> <narrative-branch>
# e.g. git rebase --onto 2.0.20 2.0.18 narrative/spark-3.5-scala-cross-build
```

Then resolve any conflicts (most live in `pom.xml` and the Scala-2.13 cross-compat source
fixes), bump the `-narrative<n>` suffix in `pom.xml`, and rebuild/publish.

Keep the narrative delta small so this rebase stays painless. It is: the `pom.xml`
cross-build wiring (version suffix, `deequ_${scala.major.version}` artifactId,
`scala-2.12`/`scala-2.13` profiles, `scala-collection-compat` dep), the Scala-2.13
cross-compilation source fixes, and three auxiliary files (`README-narrative.md`,
`.tool-versions`, `.github/workflows/backup-daily.yml` + the CI matrix in `maven.yml`).

Options to fully automate the S3 publish:
- https://github.com/ehsaniara/maven-repository-aws-s3
- https://jmchung.github.io/post/using-amazon-s3-as-a-private-maven-repository/
