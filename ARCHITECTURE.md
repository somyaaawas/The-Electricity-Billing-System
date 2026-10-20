# Electricity Billing System - Architecture Documentation

## 📋 Overview

The Electricity Billing System is a Java-based application following the **MVC (Model-View-Controller)** architecture pattern. It provides both a desktop GUI (Swing) and web-based servlet interface for managing electricity billing operations.

---

## 🏗️ System Architecture

### Architecture Pattern: MVC

```
┌─────────────────────────────────────────────────────────────┐
│                        VIEW LAYER                            │
│  ┌──────────────────┐           ┌──────────────────────┐    │
│  │   Swing GUI      │           │   JSP (Web)          │    │
│  │  - Splash.java   │           │  - login.jsp         │    │
│  │  - Login.java    │           │  - dashboard.jsp     │    │
│  │  - bill_details  │           │  - billing.jsp       │    │
│  └──────────────────┘           └──────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                              ↕
┌─────────────────────────────────────────────────────────────┐
│                     CONTROLLER LAYER                         │
│  ┌──────────────────┐  ┌──────────────────┐  ┌────────────┐ │
│  │  LoginServlet    │  │ CustomerServlet  │  │ Billing    │ │
│  │                  │  │                  │  │ Servlet    │ │
│  │ - Authentication │  │ - CRUD Ops       │  │ - Bill Gen │ │
│  │ - Session Mgmt   │  │ - Search         │  │ - Reports  │ │
│  └──────────────────┘  └──────────────────┘  └────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              ↕
┌─────────────────────────────────────────────────────────────┐
│                      MODEL/DAO LAYER                         │
│  ┌──────────────────┐           ┌──────────────────────┐    │
│  │   CustomerDAO    │           │    BillDAO           │    │
│  │                  │           │                      │    │
│  │ - add()          │           │ - generateBill()     │    │
│  │ - update()       │           │ - getBillHistory()   │    │
│  │ - delete()       │           │ - calculateAmount()  │    │
│  │ - search()       │           │ - updateBillStatus() │    │
│  └──────────────────┘           └──────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                              ↕
┌─────────────────────────────────────────────────────────────┐
│                    PERSISTENCE LAYER                         │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              MySQL Database                          │   │
│  │  - customers table                                   │   │
│  │  - bills table                                       │   │
│  │  - users table                                       │   │
│  │  - readings table                                    │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```
The-Electricity-Billing-System/
│
├── src/
│   ├── main/java/electricity/billing/system/
│   │   ├── Splash.java                    # Application entry point
│   │   ├── Login.java                     # Swing login GUI
│   │   ├── Signup.java                    # Swing signup GUI
│   │   ├── bill_details.java              # Bill details GUI
│   │   ├── customer_details.java          # Customer details GUI
│   │   ├── calculate_bill.java            # Bill calculation logic
│   │   ├── WebServer.java                 # Embedded Tomcat server
│   │   │
│   │   ├── controller/
│   │   │   ├── LoginServlet.java          # Authentication servlet
│   │   │   ├── CustomerServlet.java       # Customer CRUD servlet
│   │   │   ├── BillingServlet.java        # Billing servlet
│   │   │   └── SwingUI.java               # Swing UI controller
│   │   │
│   │   ├── dao/
│   │   │   ├── CustomerDAO.java           # Customer data access
│   │   │   └── BillDAO.java               # Bill data access
│   │   │
│   │   └── model/
│   │       ├── Customer.java              # Customer entity
│   │       ├── Bill.java                  # Bill entity
│   │       ├── User.java                  # User entity
│   │       └── Reading.java               # Meter reading entity
│   │
│   ├── db.properties                      # Database configuration
│   └── icon/                              # Application icons
│
├── WEB-INF/
│   └── web.xml                            # Servlet mappings
│
├── webapp/
│   ├── login.jsp                          # Login page
│   ├── dashboard.jsp                      # Dashboard page
│   ├── billing.jsp                        # Billing page
│   └── css/                               # Stylesheets
│
├── pom.xml                                # Maven configuration
├── setup_database.sql                     # Database initialization
├── run.sh                                 # Unix run script
└── README.md                              # Project documentation
```

---

## 🔄 Data Flow

### Swing GUI Flow (Desktop Application)

```
1. User launches application
   ↓
2. Splash screen displays (3 seconds)
   ↓
3. Login window opens
   ↓
4. User enters credentials → LoginServlet validates
   ↓
5. Authentication success → Dashboard loads
   ↓
6. User selects operation (Add/Update/Delete/View bills)
   ↓
7. CustomerServlet/BillingServlet handles request
   ↓
8. DAO layer queries database
   ↓
9. Results displayed in GUI
```

### Web Servlet Flow (Web Application)

```
1. Browser sends HTTP request
   ↓
2. Servlet receives request (LoginServlet/CustomerServlet/BillingServlet)
   ↓
3. Servlet processes business logic
   ↓
4. DAO fetches/updates data from database
   ↓
5. Servlet generates response (JSON/JSP)
   ↓
6. Response sent to browser
   ↓
7. JSP renders view or JSON displayed
```

---

## 🗄️ Database Schema

### Tables

#### `users` Table
- `user_id` (Primary Key)
- `username` (Unique)
- `password` (Hashed)
- `user_type` (Admin/Staff/Customer)
- `created_at`

#### `customers` Table
- `customer_id` (Primary Key)
- `name`
- `email`
- `phone`
- `address`
- `meter_number` (Unique)
- `connection_date`
- `status` (Active/Inactive)

#### `bills` Table
- `bill_id` (Primary Key)
- `customer_id` (Foreign Key)
- `billing_date`
- `previous_reading`
- `current_reading`
- `units_consumed`
- `amount`
- `status` (Pending/Paid)
- `due_date`

#### `readings` Table
- `reading_id` (Primary Key)
- `customer_id` (Foreign Key)
- `meter_reading`
- `reading_date`
- `reader_id`

---

## 🔐 Key Components

### 1. **Model Layer**
- **Customer.java**: Represents customer entity with properties like name, email, meter number
- **Bill.java**: Represents billing entity with calculation logic
- **User.java**: Represents system user (Admin/Staff/Customer)
- **Reading.java**: Represents meter readings

### 2. **DAO Layer**
- **CustomerDAO.java**: Handles all customer CRUD operations
  - `addCustomer()`
  - `updateCustomer()`
  - `deleteCustomer()`
  - `searchCustomer()`
  - `getAllCustomers()`

- **BillDAO.java**: Handles all billing operations
  - `generateBill()`
  - `getBillHistory()`
  - `calculateBillAmount()`
  - `updateBillStatus()`
  - `getPendingBills()`

### 3. **Controller Layer**

#### LoginServlet
- Authenticates user credentials
- Manages user sessions
- Redirects to appropriate dashboard

#### CustomerServlet
- Handles customer CRUD operations
- Processes customer search requests
- Returns customer data as JSON/HTML

#### BillingServlet
- Generates bills for customers
- Retrieves bill history
- Processes payment updates
- Exports bill reports

### 4. **View Layer**

#### Swing GUI Components
- **Splash.java**: Initial splash screen
- **Login.java**: Login window with form validation
- **bill_details.java**: Display and manage bill details
- **customer_details.java**: Display and manage customer details

#### JSP Pages
- **login.jsp**: Web-based login
- **dashboard.jsp**: User dashboard
- **billing.jsp**: Billing operations

---

## 🔄 Request/Response Cycle

### Example: Add New Customer

```
Swing GUI Input
    ↓
CustomerServlet (POST /addCustomer)
    ↓
Input Validation (null checks, format validation)
    ↓
CustomerDAO.addCustomer()
    ↓
PreparedStatement executes INSERT
    ↓
Database transaction committed
    ↓
Success response returned to GUI
    ↓
GUI displays confirmation message
```

### Example: Generate Bill

```
Billing Page Selection
    ↓
BillingServlet (POST /generateBill)
    ↓
BillDAO.generateBill()
    ↓
Read current meter reading
    ↓
Calculate units consumed
    ↓
Apply tariff rates
    ↓
Calculate total amount
    ↓
Insert bill record
    ↓
Return bill details to view
    ↓
Display bill/export PDF
```

---

## 🔐 Security Features

### Authentication & Authorization
- Username/password validation
- User session management
- Role-based access control (Admin/Staff/Customer)

### SQL Injection Prevention
- **PreparedStatements** used for all database queries
- User input parameterized

### Input Validation
- Null checks
- Format validation (email, phone)
- Range validation (meter readings, amounts)

### Exception Handling
- Custom exceptions for billing operations
- Error logging
- User-friendly error messages

---

## 🚀 Technology Stack

| Component | Technology |
|-----------|-----------|
| **Language** | Java 8+ |
| **GUI Framework** | Java Swing |
| **Web Framework** | Java Servlets + JSP |
| **Database** | MySQL 5.7+ / 8.0+ |
| **Build Tool** | Maven 3.9+ |
| **Web Server** | Embedded Tomcat 9.0 |
| **JDBC Driver** | MySQL Connector J 8.0.33 |
| **ORM Approach** | JDBC with DAO pattern |

---

## 🔄 Design Patterns Used

### 1. **MVC Pattern**
- Separates concerns into Model, View, and Controller
- Facilitates code reusability and maintainability

### 2. **DAO Pattern**
- Abstracts database operations
- Provides centralized data access
- Easy to switch database implementations

### 3. **Singleton Pattern**
- Database connection pool management
- Configuration management

### 4. **Factory Pattern**
- Object creation for servlets and DAOs

### 5. **Observer Pattern**
- Event handling in Swing GUI components

---

## 🧪 Key Features Implementation

### Multi-threading
- Concurrent bill generation
- Background PDF export
- Thread-safe resource access

### Collections & Generics
```java
ArrayList<Customer> customers;
HashMap<String, Bill> billHistory;
List<Reading> meterReadings;
```

### Exception Handling
```java
try {
    customerDAO.addCustomer(customer);
} catch (InvalidInputException e) {
    logger.error("Invalid input: " + e.getMessage());
} catch (DatabaseException e) {
    logger.error("Database error: " + e.getMessage());
}
```

---

## 📊 Database Configuration

**File**: `src/db.properties`

```properties
db.url=jdbc:mysql://localhost:3306/Bill_system
db.username=root
db.password=
db.driver=com.mysql.cj.jdbc.Driver
```

---

## 🚀 Deployment

### Development
```bash
mvn clean compile
java -cp "target/classes:target/electricity-billing-system-1.0.0/WEB-INF/lib/*" electricity.billing.system.Splash
```

### Production (Web)
```bash
mvn clean package
# Deploy WAR to Tomcat
cp target/electricity-billing-system-1.0.0.war $TOMCAT_HOME/webapps/
```

---

## 📈 Future Enhancements

1. **Online Payment Gateway**: Integrate payment APIs (PayPal, Stripe)
2. **Mobile App**: React Native mobile interface
3. **Advanced Analytics**: BI dashboards and consumption predictions
4. **Machine Learning**: Usage pattern analysis and anomaly detection
5. **Microservices**: Refactor into microservices architecture
6. **Cloud Deployment**: AWS/Azure deployment support

---

## 📝 Summary

The Electricity Billing System is a well-structured Java application demonstrating:
- ✓ Object-Oriented Programming principles
- ✓ Design patterns (MVC, DAO, Singleton)
- ✓ Database connectivity via JDBC
- ✓ GUI development with Swing
- ✓ Web application development with Servlets
- ✓ Security best practices
- ✓ Exception handling and logging
- ✓ Multi-threading capabilities

This architecture ensures scalability, maintainability, and extensibility for future enhancements.
