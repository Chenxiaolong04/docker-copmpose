# Spring Boot Hello API + Frontend (AJAX) + Docker

Questo progetto mostra un esempio base di un'applicazione Spring Boot esposta tramite Docker, con un'interfaccia web statica (HTML/JavaScript) che comunica con il backend tramite chiamate AJAX (`fetch` o `jQuery.get`).

---
## Inizializzazione del progetto Spring Boot

Per creare la struttura del progetto backend ho utilizzato [Spring Initializr](https://start.spring.io/), uno strumento online che permette di generare una base pronta all'uso per progetti Spring Boot.

Ho selezionato le seguenti dipendenze fondamentali:

- **Spring Web**: per la creazione di API REST e gestione delle richieste HTTP.
- **Spring Data JPA**: per l'interazione con il database tramite un approccio ORM.
- **MySQL Driver**: per permettere la connessione al database MySQL.

Una volta configurato il progetto, ho scaricato lo ZIP generato e lo ho importato all'interno del mio IDE (IntelliJ IDEA / Eclipse / VS Code) per iniziare lo sviluppo.

---

## Percorsi importanti

- File backend Java controller:  
  `src/main/java/it.its.esercizio/controller/HelloController.java`

- File backend configurazione CORS:  
  `src/main/java/it.its.esercizio/config/CorsConfig.java`

- File frontend HTML:  
  `index.html` (nella root del progetto)

- File Dockerfile:  
  `Dockerfile` (nella root del progetto)

- File Maven di build:  
  `pom.xml` (nella root del progetto)

## Requisiti

- Java 17
- Docker installato
- Node.js + npm installati (per avviare il frontend)
- Maven (solo se vuoi buildare manualmente il backend fuori da Docker)

---

## 1. Costruire l'immagine Docker del backend

Assicurati che il file `Dockerfile` si trovi nella radice del progetto e che contenga qualcosa di simile a questo:

```Dockerfile
FROM maven:3-eclipse-temurin-17 AS build

WORKDIR /workspace
COPY pom.xml .
COPY src ./src
RUN mvn clean package

FROM openjdk:17-jdk-slim
WORKDIR /app
COPY --from=build /workspace/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "/app/app.jar"] 
```
Apri il terminale nella cartella del progetto e lancia:
```
docker build -t myapp:1.0 .
```
## 2. Avvio del container Docker
Esegui il container esponendo la porta:
```
docker run -p 8081:8080 myapp:1.0
```
## 3. Avvio del frontend
Se hai serve installato:
```
npx serve -p 3000 .
```
Oppure, se non lo hai:
```
npm install -g serve
serve -p 3000 .
```
## 4. Funzionamento
1. Inserisci un nome nel campo di input.

2. Premi il pulsante "Ciao".

3. Verrà inviata una richiesta GET al backend (/hello/greeting?name=...).

4. Il risultato sarà mostrato sotto al bottone.
## 5. Configurazione CORS
Per abilitare il frontend a comunicare col backend da un dominio diverso (localhost:3000), è stato aggiunto il file CorsConfig.java:
```java
@Configuration
public class CorsConfig {
    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**")
                        .allowedOrigins("http://localhost:3000")
                        .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                        .allowedHeaders("*");
            }
        };
    }
}

```
## 6. Test diretto dell'API
Puoi testare il backend anche senza frontend:

Apri il browser o usa curl:
```
http://localhost:8081/hello/greeting?name=xiaolong
```
Risposta attesa:
```
Hello, xiaolong!
```
