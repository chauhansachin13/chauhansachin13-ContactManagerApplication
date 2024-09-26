# chauhansachin13-ContactManagerApplication

Project Structure
Here's the suggested directory structure for your project:

css
Copy code
contact-manager/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── contactmanager/
│   │   │               ├── ContactManagerApplication.java
│   │   │               ├── Contact.java
│   │   │               ├── ContactRepository.java
│   │   │               └── ContactController.java
│   │   └── resources/
│   │       └── application.properties
├── frontend/
│   ├── index.html
│   └── app.js
├── pom.xml
└── README.md
Java Code
Here’s the full code for your Java Spring application.

ContactManagerApplication.java

java
Copy code
package com.example.contactmanager;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ContactManagerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ContactManagerApplication.class, args);
    }
}
Contact.java

java
Copy code
package com.example.contactmanager;

import javax.persistence.*;

@Entity
public class Contact {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    private String phone;
    private String address;

    // Getters and Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public String getPhone() { return phone; }
    public void setPhone(String phone) { this.phone = phone; }
    public String getAddress() { return address; }
    public void setAddress(String address) { this.address = address; }
}
ContactRepository.java

java
Copy code
package com.example.contactmanager;

import org.springframework.data.jpa.repository.JpaRepository;

public interface ContactRepository extends JpaRepository<Contact, Long> {}
ContactController.java

java
Copy code
package com.example.contactmanager;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/contacts")
public class ContactController {
    @Autowired
    private ContactRepository contactRepository;

    @GetMapping
    public List<Contact> getAllContacts() {
        return contactRepository.findAll();
    }

    @PostMapping
    public Contact createContact(@RequestBody Contact contact) {
        return contactRepository.save(contact);
    }

    @GetMapping("/{id}")
    public Contact getContactById(@PathVariable Long id) {
        return contactRepository.findById(id).orElse(null);
    }

    @PutMapping("/{id}")
    public Contact updateContact(@PathVariable Long id, @RequestBody Contact contactDetails) {
        return contactRepository.findById(id).map(contact -> {
            contact.setName(contactDetails.getName());
            contact.setEmail(contactDetails.getEmail());
            contact.setPhone(contactDetails.getPhone());
            contact.setAddress(contactDetails.getAddress());
            return contactRepository.save(contact);
        }).orElse(null);
    }

    @DeleteMapping("/{id}")
    public void deleteContact(@PathVariable Long id) {
        contactRepository.deleteById(id);
    }
}
Application Properties
application.properties

properties
Copy code
spring.datasource.url=jdbc:mysql://localhost:3306/contact_manager
spring.datasource.username=<your_username>
spring.datasource.password=<your_password>
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
Frontend Code
index.html

html
Copy code
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Contact Manager</title>
    <script src="app.js" defer></script>
</head>
<body>
    <h1>Contact Manager</h1>
    <div>
        <h2>Add Contact</h2>
        <input type="text" id="name" placeholder="Name">
        <input type="email" id="email" placeholder="Email">
        <input type="text" id="phone" placeholder="Phone">
        <input type="text" id="address" placeholder="Address">
        <button id="addContactButton">Add Contact</button>
    </div>

    <h2>Contact List</h2>
    <ul id="contactList"></ul>
</body>
</html>
app.js

javascript
Copy code
document.getElementById("addContactButton").addEventListener("click", function() {
    const contact = {
        name: document.getElementById("name").value,
        email: document.getElementById("email").value,
        phone: document.getElementById("phone").value,
        address: document.getElementById("address").value
    };

    fetch("/api/contacts", {
        method: "POST",
        headers: {
            "Content-Type": "application/json"
        },
        body: JSON.stringify(contact)
    })
    .then(response => response.json())
    .then(data => {
        console.log("Contact added:", data);
        loadContacts();
    });
});

function loadContacts() {
    fetch("/api/contacts")
    .then(response => response.json())
    .then(data => {
        const contactList = document.getElementById("contactList");
        contactList.innerHTML = "";
        data.forEach(contact => {
            const li = document.createElement("li");
            li.textContent = `${contact.name} - ${contact.email} - ${contact.phone} - ${contact.address}`;
            contactList.appendChild(li);
        });
    });
}

loadContacts();
Maven Configuration
pom.xml

xml
Copy code
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>contact-manager</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <java.version>11</java.version>
        <spring.version>2.5.6</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
README.md
Now, here’s the content for your README.md file:

markdown
Copy code
# Contact Manager Application

A simple Contact Manager application developed using Spring Boot, Hibernate, and MySQL for backend, and HTML/JavaScript for the frontend.

## Features

- Create, read, update, and delete contacts
- RESTful API for contact management
- Simple frontend interface for interaction

## Technologies Used

- **Backend**: Java, Spring Boot, Hibernate, MySQL
- **Frontend**: HTML, JavaScript

## Getting Started

### Prerequisites

- Java JDK 11 or higher
- Maven
- MySQL Server

### Setting Up the Database

1. Create a new database in MySQL:

   ```sql
   CREATE DATABASE contact_manager;
Update src/main/resources/application.properties with your MySQL username and password:

properties
Copy code
spring.datasource.url=jdbc:mysql://localhost:3306/contact_manager
spring.datasource.username=<your_username>
spring.datasource.password=<your_password>
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
Building and Running the Application
Navigate to the project root directory:

bash
Copy code
cd contact-manager
Build the application with Maven:

bash
Copy code
mvn clean install
Run the application:

bash
Copy code
mvn spring-boot:run
Open your web browser and navigate to http://localhost:8080/.

API Endpoints
GET /api/contacts - Retrieve all contacts
POST /api/contacts - Add a new contact
GET /api/contacts/{id} - Retrieve a contact by ID
PUT /api/contacts/{id} - Update a contact by ID
DELETE /api/contacts/{id} - Delete a contact by ID
Frontend Interaction
Open frontend/index.html in your web browser to interact with the contact manager interface.
License
This project is licensed under the MIT License.

sql
Copy code

### Conclusion
With this setup, you have a complete Contact Manager application that utilizes Java, Spring, Hibernate, MySQL, and JavaScript. Simply create the required files and folders in your local environment, copy and paste the provided code, and you should be ready to go!

Feel free to modify any parts of the README or project structure to better fit your needs or 
