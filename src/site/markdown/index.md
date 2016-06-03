## Lightweight RDF Annotations

The [rdf-annotations module](rdf-annotations) provides support for mapping Java classes and member fields to OWL RDF.  

  * [Homepage](http://emetsger.github.io/osf-rdf/1.0.0-SNAPSHOT/)
  * [Javadoc](http://emetsger.github.io/osf-rdf/1.0.0-SNAPSHOT/apidocs/index.html)

## Shortcomings
This library is a work-in-progress, and does not cover every imaginable use case when mapping Java domain models to RDF.  Furthermore, this library supports a specific domain model and is not generally applicable to arbitrary domains.

## Annotations

This library currently provides the following annotations:

  * `@OwlIndividual`: annotates Java classes.  Instances of the annotated Java class will be mapped to OWL individuals.  The value of the _annotation_ specifies the OWL class of the individual.
  * `@IndividualId`: annotates Java fields.  The value of the _annotated field_ will be used as the RDF resource identifier of the enclosing `@OwlIndividual`.  Classes annotated with `@OwlIndividual` are expected to have _exactly one_ member annotated with `@IndividualId`.
  * `@OwlProperty`: annotates Java fields.  Annotated fields will be mapped to OWL properties. The value of the _annotation_ specifies the OWL property used, and indicates whether it is an OWL Datatype or Object property.  The subject of the OWL property will be the enclosing `@OwlIndividual`, and the object of the OWL property will be the value of the _annotated field_.  Classes annotated with `@OwlIndividual` may have _zero or more_ members annotated with `@OwlProperty`.  

## Transforms

The `@IndividualId` and `@OwlProperty` annotations carry a `transform` attribute, specifying a Class of `Function<Object, String>`.  Annotation processors are expected to instantiate the specified `Function`, and `apply(..)` it to the value of the _annotated field_ prior to RDF serialization.

