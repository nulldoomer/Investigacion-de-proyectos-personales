# Most relevant knowledge obtained

I've obtained some serious architectural knowledge through the development of the SDK specifically, because I used [[domain-driven-design-moc]] as the main architecture design for the SDK, and also it's combined with the layer architecture, giving me the resultant project structure:

![[Pasted image 20260513163254.png]]

Just for the JAVA SDK, here I'd try to do a general overview of anything that I've done on this 4 months of development, and what I can improve to be better.

## API

Here I have the user presentation layer, where is the entry point of the user experience, as I decided to make an SDK for the developer community, being more simple and structured for a third party implementation on their systems, more than an API, I made the creation of the main objects easy for the user to implement, so I made it with the **_Fluent Builder Pattern_**, for the client which allows the user to set the issuer information as the owner of the business, and information about the environment, etc..., and also just the **_Invoice_** (By now), creating the object covering all the edge cases for this, and making it more simple for the user to calculate, totals, taxes, etc...

The **_OPENSRIClient_** is the main orchestrator being the composition root which instances and wire every dependency on its constructor, to manage all the process flow of creating, sending, capturing SRI responses.

## APPLICATION

This is the public layer where it lives the ports interfaces that describes the contracts of the services to the user (Here the OPENSRIClient), and the **_usecases_** which orchestrates the ports implementations to create the flow of a process, existing the following **_SendInoviceUseCase_**, **_CheckAuthorizationUseCase_**.

## DOMAIN

Where all the records of the main entities, enums, value objects and responses exists, the layer where is all the non-mutable objects, applying [[ddd-value-objects]], [[ddd-aggregate-root]] concepts to keep the immutability.

## INFRASTRUCTURE

This is where it lives all the specific implementations of the ports that are exposed on the application layer, made it package private to secure and cover the implementations which the user never has to know about it, the unique contract between the user is the ports (interfaces), the adapters code is encapsulated here.

## SHARED-EXCEPTIONS

The custom shared exceptions through the application, not relying on standard exceptions, customizing all the messages and throwing exceptions.

# SPECIFIC KNOWLEDGE

### FLUENT BUILDER PATTERN

This was knew to me and I learned deep to set my way of creating the main object of my application, creating every step interface and it was a little confusing at the first time because of the way of creating the interface, the methods has to be typed as the next step interface if it's optional it has to be the same type and the final class where all converges.

### TOTALS CALCULATE

An static method in the Total record which it calculates all the total values for the final invoice object, doing this was difficult because you got to understand how the taxes, and the total values operates, first got to create a look up table separating the taxes in groups, that got the tax name and the total amount of tax of all the products for that specific tax:
![[Pasted image 20260513175423.png]]

And then after extract all the values from that look up table to create the list of totals, taking the keys of the group to create a Total Tax for the list:

The way of using a look up table was so similar to the [[Two Sum]] resolution approach.
![[Pasted image 20260513180338.png]]

```java
  for (Map.Entry<String, TaxAccumulator> entry : taxesGroups.entrySet()) {

    String[] parts = entry.getKey().split("-");
    String code = parts[0];
    String rateCode = parts[1];

    TaxAccumulator acc = entry.getValue();

    totalTaxValue = totalTaxValue.add(acc.value());

    totalTaxes.add(new TotalTax(code, rateCode, acc.base(), acc.value()));
  }
  BigDecimal totalValue =
      totalWithoutTaxes.subtract(totalDiscount).add(totalTaxValue).add(totalTipValue);

  return new Totals(totalValue, totalWithoutTaxes, totalDiscount, totalTipValue, totalTaxes);
}
```

### SEALED INTERFACE

I had to create a **_sealed interface_** for the payment entity and the client identification on the value objects:
![[Pasted image 20260513180849.png]]
![[Pasted image 20260513180925.png]]

This is mainly to control all the posible implementations, the invariants, and all the validations of a closed domain.

### CRYPTO

My first time with cryptography signing keys for the XML, I created a loader for getting the necessary information for signing take it from the `.p12` certificate, the information take it is:![[Pasted image 20260513183729.png]]

- XAdES -> XML Advanced Electronic Signatures

This is the electronic signature standard for XML documents, this allows to add legal metadata, certificate information and politics, the information that I take for the XSD validation of the signature as long as I know in Ecuador is the **_private key_** and the **_X509Certificate_**

- BES -> Basic Electronic Signature

This is the profile of the XAdES for the SRI standard, here it creates the profile:
![[Pasted image 20260513184539.png]]

I got troubles testing the signature because it wasn't signing on the root node of the XML, because of the "#comprobante"
![[Pasted image 20260513184735.png]]
which states to the signature the main node of the invoice that has this: specific `id="#comprobante"`.

And this features for the final signed XML to avoid issues and errors:
![[Pasted image 20260513184952.png]]

### XML BINDING

To bind the domain entities from the application using JAXB for the XML serialization first it has to create the schemas as JAXB objects. I used the binding annotations @XmlRootElement
@XmlAccessorType, @XmlAttribute y @XmlElementWrapper, @XmlElement

### XML SERIALIZATION

First of all the port interface is a parametrized interface to be able to grow in the future with other documents making this approach scalable through all the documents that SRI includes, here it creates the JAXB context, the marshaller concept that is used to serialize the dto class to an XML.

### SRIAccessKeyGenerator

Here it creates the access key for every document, every access key is different here I used something tricky here, I used the system current miliseconds and a random using ThreadLocalRandom, and the module11 to create the verification digit.

### SRI GATEWAY

For this at the first time I trusted on the SRI wsdl, having some troubles with the responses, then I decided to switch to my own implementation using an Http client to connect with the web service of the SRI.

Creating myself the request and catching the response, for the reception and the authorization endpoints given by the SRI, using a utility class to get the urls, also I receive the responses as a string with soap xml format, to resolve this to my domain records of the responses I had to create mappers, so here I understood how it works the class **_Document_** which was used before on the xml serializer it creates a **_Tree Data Structure_** of the xml format creating a node for every element. the thought process is explained on [[RECEIPT PARSING.excalidraw]], [[AUTHORIZATION PARSING.excalidraw]]. I created 2 classes for parsing and the xml utils to separate the responsibilities, and then operate the methods on my mappers. Also discovered that the final classes in which I use static methods to achieve specific tasks without instantiating the classes because they're static, and to enforce that putting final on the class and creating a private constructor makes sure to the compilation that the class will never be instantiated.

![[Pasted image 20260513225910.png]]

### TESTING

Here I finally took the time to understand the testing and also read [[UNIT TESTING PRINCIPLES, PRACTICES AND PATTERNS]] to understand the testing, I created unit testing, using the 3A approach Arrange, Act, Assert. Discovering the @BeforeEach, @DisplayName. Know the difference between Unit, Integration and property testing. Also applied mocking to the unit testing, finally decided to delete the mock test because it worked more for my case the integration test.

And that's anything that I've learned on this journey of 4 months of development. Right now I'm on the the final stage of development having the **_use cases, OPENSRIClient_** implementations left, and that would end the development for the JAVA SDK, then I'll have to start with the frontend for the SDK platform, publish the SDK as a JAVA library, finish the platform first approach with Java or Go backend, and then create the portfolio and publish everywhere.
