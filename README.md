# Shoppinator - Online Store Price Comparison System

Shoppinator is a system designed to optimize online shopping by allowing users to compare prices across different online stores. The system is built with design patterns to ensure flexibility, scalability, and the ability to add new stores dynamically without modifying the core system.

## Project Structure

This repository is divided into three main submodules, each representing a key component of the system:

- **[UI](https://github.com/FrancoBre/pp2-gui/tree/iteracion-1)**: The user interface for interacting with the system.
- **[CORE](https://github.com/maxisandoval37/pp2-core/tree/iteracion-1)**: The core logic of the system, which handles the model and business rules.
- **[EXT](https://github.com/FrancoBre/pp2-ext/tree/stores-scrapers)**: Extensions that allow for the addition of new stores in a modular way.

---

## 1. Product Search Across Multiple Stores

Shoppinator allows users to search for products across multiple online stores. The system retrieves product details such as the name, price, and image from each store's website using web scraping.

- **Pattern Used: Plugin Pattern**  
  Each store is represented by a `Shop` class, following the **Plugin Pattern** from [PEAA]. This design enables the dynamic loading of new stores at runtime, allowing the system to be easily extended with minimal changes to the core. The `Shop` classes encapsulate the store-specific logic for product searching and parsing the HTML results.

- **Implementation Details**:  
  We used **JSoup** to scrape store websites like *Frávega*. The system extracts relevant product data using HTML selectors, such as:

  ```java
  String name = articleElement.select("div.product-card-design6-vertical__name").text();
  ```

---

Additionally, URL queries (e.g., ?keyword=mouse) were leveraged to optimize the search process, reducing resource consumption and speeding up response times.

## 2. Price Range Filtering
The system allows users to filter the search results by setting a minimum and/or maximum price. This enables more refined searches based on user-defined criteria.

 - **Pattern Used: Criteria Pattern**
The price filtering functionality is implemented using the Criteria Pattern from [GOF]. A PriceSearchCriteria object is created during the initialization of Shoppinator. The PriceSearchCriteria works as an independent, modular component that can filter products based on price and can easily be extended to handle other criteria in the future.

 - **Implementation Details:**
When a search query is executed, PriceSearchCriteria filters the products by invoking:

```java
List<Article> filteredProducts = priceCriteria.apply(products);
```

This ensures that only the products matching the price range are returned.
---

## 3. Select Specific Stores for Product Search
Shoppinator allows users to specify which stores to include in the search. This feature gives users control over which stores' products are considered in the final results.

 - **Pattern Used: Factory Pattern & Observer Pattern**
The Factory Pattern is used to instantiate Shoppinator and load the available stores dynamically. When ShoppinatorFactory is invoked, it performs a Discovery Process to load the stores from a given path. The available stores are stored in a map, and users can select specific stores for searches.

The Observer Pattern is used to track changes in the available stores. ShoppinatorCore observes any changes to the stores and updates the active store list accordingly.

 - **Implementation Details:**
When the user selects stores, the system updates the internal state using:

```java
shoppinator.setShops("Frávega", "Garbarino");
```

The selected stores are then used for product searches.

## **4. Dynamic Product List Updates**
The system can automatically update the product list by detecting changes in the stores, such as new products being added. This feature ensures that the product list remains up-to-date without manual intervention.

 - **Pattern Used: Observer Pattern**
The Observer Pattern is heavily used in this feature. Each store (Shop) is observable by Shoppinator. When a store detects a change in its product list, it notifies Shoppinator, which in turn updates the UI with the new products.

 - **Implementation Details:**
Stores send notifications when there is an update:

```java
shop.notifyObservers();
```

Shoppinator listens for these changes and updates the product list accordingly.

## **5. Modular Store Loading with Discovery**
The system uses a Discovery process to dynamically load stores at runtime. This allows the system to remain flexible, as new stores can be added without modifying the core logic.

 - **Pattern Used: Plugin & Factory Pattern**
The Plugin Pattern allows each store to be encapsulated in a separate Shop class, which is loaded dynamically during the discovery process. The Factory Pattern is responsible for instantiating these Shop classes at runtime.

 - **Implementation Details:**
The discovery process uses the following pseudocode to find and load new stores:

```plaintext
function discoverShops(path):
    for each file in path:
        if file is a valid .jar:
            load class from file
            if class is a Shop:
                instantiate and add to the system
```

This approach ensures that new stores can be integrated without recompiling or altering the existing system.

## **6. Package and Component Structure**
The system is organized into several packages to maintain a clear separation of concerns and modularity:

 - `entities`: Contains core entities, such as Article and Shop.
 - `service` : Encapsulates service logic:
 - `service` .assembly: Responsible for assembling articles from product data.
 - `service` .discovery: Handles the dynamic discovery of stores.
 - `service` .factory: Creates instances of Shoppinator.
 - `shoppinator.core`: Contains the core logic for searching products across multiple stores.

---
## **Continuous Integration**
This project uses continuous integration (CI) to ensure code stability and automated testing. The workflow can be found here:

 - Continuous Integration (CI)
---

## **Installation**
To clone the repository with submodules:

```bash
git clone --recurse-submodules git@github.com:FrancoBre/shoppinator.git
```

If you have already cloned the repository without submodules:

```bash
git submodule update --init --recursive
```

## **Running the Applications**
All projects (core, UI, and extensions) are Java projects managed with Gradle. The steps to build and run any of these projects are the same:

Navigate to the directory of the project you want to run (either core, ui, or ext).

Build the project:

```
./gradlew build
```

This will compile the code, resolve dependencies, and create the necessary build files for the application.

Run the project:
```
./gradlew bootRun
```

This will start the corresponding application, whether it’s the core, UI, or extensions.
