# DevOps::maven

## maven: get effective POM

```
mvn help:effective-pom \
  -Doutput=effective-pom.xml
```


## maven: dependency tree

```
mvn dependency:tree
```


## maven: dependency tree of an specific package

```
mvn dependency:tree \
  -Dincludes=com.some.package
```

