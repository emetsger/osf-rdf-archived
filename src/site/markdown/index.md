## Lightweight RDF Annotations

The [rdf-annotations module](rdf-annotations) provides support for mapping Java classes and member fields to OWL RDF.  

  * [Homepage](http://emetsger.github.io/osf-rdf/1.0.0-SNAPSHOT/)
  * [Javadoc](http://emetsger.github.io/osf-rdf/1.0.0-SNAPSHOT/apidocs/index.html)

## Shortcomings

This library is a work-in-progress, and does not cover every imaginable use case when mapping Java domain models to RDF.  Furthermore, this library supports a specific domain model and is not generally applicable to arbitrary domains.  Finally, if the developer does not have access to the source code of the model being annotated, this annotation module is not very useful at this point, due to limitations on where the annotated elements may appear (e.g. a `@OwlIndividual` is a class-level annotation only).

## Annotations

This library currently provides the following annotations:

  * `@OwlIndividual`: annotates Java classes.  Instances of the annotated Java class will be mapped to OWL individuals.  The value of the _annotation_ specifies the OWL class of the individual.
  * `@IndividualId`: annotates Java fields.  The value of the _annotated field_ will be used as the RDF resource identifier of the enclosing `@OwlIndividual`.  Classes annotated with `@OwlIndividual` are expected to have _exactly one_ member annotated with `@IndividualId`.
  * `@OwlProperty`: annotates Java fields.  Annotated fields will be mapped to OWL properties. The value of the _annotation_ specifies the OWL property used, and indicates whether it is an OWL Datatype or Object property.  The subject of the OWL property will be the enclosing `@OwlIndividual`, and the object of the OWL property will be the value of the _annotated field_.  Classes annotated with `@OwlIndividual` may have _zero or more_ members annotated with `@OwlProperty`.
  * `@AnonIndividual`: annotates Java fields.  Instances of the _annotated field_ will be mapped to OWL anonymous individuals.  The value of the _annotation_ specifies the OWL class of the individual.  Classes annotated with `@AnonIndividual` are expected to be annotated with `@OwlProperty` which determines the property used to relate the enclosing `@OwlIndividual` to the `@AnonIndividual`.

## Transforms

The `@IndividualId` and `@OwlProperty` annotations carry two attributes: `transform` and `mode`.  Annotation processors are expected to instantiate the specified `Function` specified by the `transform` attribute and `apply(..)` it to the value of the _annotated field_ based on the specified `TransformMode`, prior to RDF serialization.

### Transformation Use Cases

Transforms are useful for type mapping or value tranformation.  

If your Java model represents timestamps using a Joda DateTime object, you can use a transformation function to convert it to a Java Calendar instance which will be typed as an xsd:dateTime type in the RDF model.

If your Java model encodes identifiers using full URLs, you can use a transformation function to decode the unique id from the URL and use it as a RDF resource identifier.

### Class transforms vs Field transforms

Transformer classes that are annotated as `mode = TransformMode.FIELD` will be applied to the value of the annotated field.  That is, the argument `t` for `apply(T t)` will be the value of the annotated field.

Transformer classes that are annotated as `mode = TransformMode.CLASS` will be applied to the value of instance of the class that declares the annotated field.  In the example below, the `ProviderIdTransform` is a class transform, so it receives an instance of the class that declares the annotated field.  The argument `t` for `apply(T t)` will be the instance of the class declaring the annotated field, in this example, a `File`.

```java
public class File {

  @OwlProperty(value = OwlProperties.OSF_HAS_ID, transform = ProviderIdTransform.class, mode = TransformMode.CLASS)
  private String id;
  
  private String provider;
  
  // ...
}

public class ProviderIdTransform implements Function<File, String> {
  @Override
  String apply(File f) {
    return f.getProvider() + ":" f.getId();
  }
}
```
