# Integrationstests

## Lernziele

- [ ] Verstehen des Unterschieds zwischen Integrationstests und Unit-Tests
- [ ] Wissen, warum Integrationstests zusätzlich zu Unit-Tests wichtig sind
- [ ] Anwendung der `@AutoConfigureMockMvc`, `@SpringBootTest`, `@Autowired`, und `@DirtiesContext` Annotations
- [ ] Verwendung von `MockMvc` zum Simulieren von HTTP-Anfragen
- [ ] Überprüfen der erwarteten Antworten mit `andExpect()`

## Integrationstests in Spring Boot

Integrationstests sind Tests, die die einzelnen Komponenten der Anwendung zusammenfügen und testen, ob sie richtig zusammenarbeiten. Im Gegensatz zu Unit-Tests, die nur einzelne Komponenten isoliert testen, testen Integrationstests das Zusammenspiel verschiedener Komponenten.

> 💡 Metapher aus dem realen Leben: Stellen Sie sich vor, Sie bauen ein Auto (die Anwendung) und testen jedes Einzelteil (Unit-Test). Integrationstests hingegen testen, ob alle Teile ordnungsgemäß zusammengebaut sind und das Auto wie erwartet funktioniert.

## Warum sind Integrationstests wichtig?

Integrationstests sind von großer Bedeutung, da sie sicherstellen, dass die Funktionalität der gesamten Anwendung getestet wird. Während Unit-Tests die einzelnen Komponenten absichern, prüfen Integrationstests, ob alle Komponenten korrekt miteinander interagieren und das erwartete Verhalten aufweisen. Diese Tests gewährleisten, dass die Anwendung als Ganzes richtig funktioniert und bieten eine zusätzliche Sicherheitsschicht.

## MockMvc in Spring Boot

`MockMvc` ist ein leistungsstarkes Werkzeug, das in Spring Boot verwendet wird, um HTTP-Anfragen zu simulieren und die Antworten zu überprüfen.

> 💡 Tipp: `MockMvc` eignet sich ideal für Integrationstests, da es die vollständige Anwendung, einschließlich der Controller, testen kann, ohne dass eine tatsächliche Serverinstanz gestartet werden muss.

## Code-Beispiele

### Wenn wir eine leere Liste erwarten

```java
@Test
void getAllProducts_shouldReturnEmptyList_whenRepositoryIsEmpty() throws Exception {
    mvc.perform(get("/shop/products"))
            .andExpect(status().isOk())
            .andExpect(content().json("[]"));
}
```

### Wenn wir eine Liste mit einem Objekt erwarten

```java
@Autowired
private MockMvc mvc;

@Autowired
private ProductRepository productRepository;

@DirtiesContext
@Test
void getAllProducts_shouldReturnListWithOneObject_whenOneObjectWasSavedInRepository() throws Exception {
    Product product = new Product("Mayo", 1);
    productRepository.save(product);

    mvc.perform(get("/shop/products"))
            .andExpect(status().isOk())
            .andExpect(content().json(
                    """
                    [
                     {
                         "name": "Mayo",
                         "id": "1"
                     }
                    ]
                    """
            ));
}
```

### Wenn wir ein Objekt hinzufügen wollen

```java
@DirtiesContext
@Test
void addProduct_shouldReturnCreatedProduct() throws Exception {
    mvc.perform(post("/shop/products")
            .contentType(MediaType.APPLICATION_JSON)
            .content(
                    """
                     {
                          "name": "Mayo",
                          "id": "1"
                     }
                    """
            ))
            .andExpect(status().isOk())
            .andExpect(content().json(
                    """
                    [
                     {
                         "name": "Mayo",
                         "id": "1"
                     }
                    ]
                    """
            ));
}
```

## Ressourcen

- [Baeldung Seite zum Thema](https://www.baeldung.com/spring-boot-testing#unit-testing-with-webmvctest)
- [Official Javadoc](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html)
- [Beispielcode](https://github.com/neuefische/hh-java-23-1-integration-test/blob/main/src/test/java/de/neuefische/integrationtest/controller/ShopIntegrationTest.java)
