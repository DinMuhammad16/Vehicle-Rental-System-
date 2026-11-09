Vehicle Rental System (Java)
A small, console-based vehicle rental system written in Java. It demonstrates object-oriented programming concepts such as abstraction, inheritance, encapsulation, and polymorphism.
Features
Add vehicles to the agency fleet.
Register and display customers.
Prevent duplicate vehicle and customer IDs.
Rent an available vehicle to a registered customer.
Calculate a rental invoice based on the vehicle's daily rate and rental duration.
Add an extra daily charge for air-conditioned cars.
Return rented vehicles and mark transactions as completed.
Prevent customer removal while that customer has an active rental.
Display vehicle availability and customer records.
Validate rental duration and report missing IDs.
Requirements
Java Development Kit (JDK) 11 or later.
A terminal / command prompt.
Check that Java is installed:
java -version
javac -version
Project structure
vehicle-rental-system/
├── Main.java
├── README.md
└── .gitignore
The project uses one source file for simplicity. Main.java contains the Vehicle abstract class, Car, Customer, RentalTransaction, RentalAgency, and the Main entry point.
Run the project
Download or clone this repository.
Open a terminal in the project folder.
Compile the program:
javac Main.java
Run it:
java Main
The demo registers two customers and two cars, rents a car to Ali, demonstrates that a customer with an active rental cannot be removed, returns the car, and then removes the customer successfully.
Example rental calculation
For a car with a base rate of Rs. 4,000 per day, air conditioning adds Rs. 500 per day. A 3-day rental costs:
(Rs. 4,000 + Rs. 500) × 3 days = Rs. 13,500
A car without air conditioning is charged only its base daily rate.
Using the classes
Register a customer:
Customer customer = new Customer("CUST03", "Sara Ali", "0301-0000000");
agency.registerCustomer(customer);
Add a car:
agency.addVehicle(new Car("CAR003", "Suzuki Swift", 3000, false));
Rent a vehicle (the customer must already be registered):
agency.rentVehicle("CAR003", "CUST03", 2);
Return it:
agency.returnVehicle("CAR003");
Notes and limitations
This is an educational in-memory demo. Data is not saved when the program exits.
It currently includes a Car subtype; other vehicle types can be added by extending Vehicle.
The sample program runs a fixed demonstration rather than offering an interactive menu.
Amounts are represented with double for simplicity. Production billing systems should generally use BigDecimal.
There is no database, authentication, or graphical user interface.
GitHub repository setup
Create an empty repository on GitHub, then run these commands from this folder (replace the URL with your own repository URL):
git init
git add .
git commit -m "Initial commit: vehicle rental system"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/vehicle-rental-system.git
git push -u origin main