# Paleo  [![Build Status](https://travis-ci.org/netzwerg/paleo.svg?branch=master)](https://travis-ci.org/netzwerg/paleo) [ ![Download](https://api.bintray.com/packages/netzwerg/maven/paleo/images/download.svg) ](https://bintray.com/netzwerg/maven/paleo/_latestVersion)

Immutable Java 8 data frames with typed columns.

A data frame is composed of `0..n` named columns, which all contain the same number of row values. Each column has a fixed
data type, which allows for type-safe value access. The following column types are supported out-of-the-box:

* **Int**: Primitive `int` values
* **Double**: Primitive `double` values
* **Boolean**: Primitive `boolean` values
* **String**: `java.lang.String` values
* **Timestamp**: `java.time.Instant` values
* **Category**: Categorical `String` values (aka factors)

Columns can be created via a fluent builder API, or populated from text files.

# Hello Paleo

The `paleo-core` module provides all classes to identify, create, and structure typed columns: 

```java
// Type-safe column identifiers
final StringColumnId NAME = ColumnIds.stringCol("Name");
final CategoryColumnId COLOR = ColumnIds.categoryCol("Color");
final DoubleColumnId SERVING_SIZE = ColumnIds.doubleCol("Serving Size (g)");

// Builder API for convenient column creation
StringColumn nameColumn = StringColumn.builder(NAME).addAll("Banana", "Blueberry", "Lemon", "Apple").build();
CategoryColumn colorColumn = CategoryColumn.builder(COLOR).addAll("Yellow", "Blue", "Yellow", "Green").build();
DoubleColumn servingSizeColumn = DoubleColumn.builder(SERVING_SIZE).addAll(118, 148, 83, 182).build();

// Straight-forward data frame creation
DataFrame dataFrame = new DataFrame(nameColumn, colorColumn, servingSizeColumn);

// Typed random access to individual values (based on rowIndex / columnId)
String lemon = dataFrame.getValueAt(2, NAME);
double appleServingSize = dataFrame.getValueAt(3, SERVING_SIZE);

// Typed stream-based access to all values
DoubleStream servingSizes = servingSizeColumn.getValues();
double maxServingSize = servingSizes.summaryStatistics().getMax();

// Smart column implementations
Set<String> colors = colorColumn.getCategories();
```

# Parsing From Text / File

The `paleo-io` module parses data frames from tab-delimited text representations:

```java
final String EXAMPLE =
            "Name\tColor\tServing Size (g)\n" +
            "String\tCategory\tDouble\n" +
            "Banana\tYellow\t118\n" +
            "Blueberry\tBlue\t148\n" +
            "Lemon\tYellow\t83\n" +
            "Apple\tGreen\t182";

DataFrame dataFrame = Parser.parseTabDelimited(new StringReader(EXAMPLE));

// Lookup typed identifiers by column index
final StringColumnId NAME = dataFrame.getColumnId(0, ColumnType.STRING);
final CategoryColumnId COLOR = dataFrame.getColumnId(1, ColumnType.CATEGORY);
final DoubleColumnId SERVING_SIZE = dataFrame.getColumnId(2, ColumnType.DOUBLE);

// Use identifier to access columns & values
StringColumn nameColumn = dataFrame.getColumn(NAME);
List<String> nameValues = nameColumn.getValues();

// ... or access individual values via row index / column id 
String yellow = dataFrame.getValueAt(2, COLOR);
```

# Usage

All modules are available via [Bintray/JCenter](https://bintray.com/netzwerg/maven/paleo/view).

## Repository Configuration

Gradle:

```groovy
repositories {
    jcenter()
}
```

Maven `settings.xml`:

```xml
<repository>
    <snapshots>
      <enabled>false</enabled>
    </snapshots>
    <id>central</id>
    <name>bintray</name>
    <url>http://jcenter.bintray.com</url>
</repository>
```

## Using the `paleo-core` module

Gradle:

```groovy
compile 'ch.netzwerg:paleo-core:0.2.1'
```

Maven:

```xml
<dependency>
    <groupId>ch.netzwerg</groupId>
    <artifactId>paleo-core</artifactId>
    <version>0.2.1</version>
    <type>jar</type>
</dependency>
```

## Using the `paleo-io` module

Optional (requires `paleo-core`)

Gradle:

```groovy
compile 'ch.netzwerg:paleo-io:0.2.1'
```

Maven:

```xml
<dependency>
    <groupId>ch.netzwerg</groupId>
    <artifactId>paleo-io</artifactId>
    <version>0.2.1</version>
    <type>jar</type>
</dependency>
```

# Why The Name?

The backing data structures are all about **raw** values and **primitive** types &mdash; this somehow reminded me of
the paleo diet.

&copy; 2015 Rahel Lüthy
