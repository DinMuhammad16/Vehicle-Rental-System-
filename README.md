
import java.util.ArrayList;
import java.util.List;

// =====================================================
// 1. VEHICLE CLASS
// =====================================================
abstract class Vehicle {
    private String vehicleId;
    private String model;
    private double baseRatePerDay;
    private boolean isAvailable;

    public Vehicle(String vehicleId, String model, double baseRatePerDay) {
        if (vehicleId == null || vehicleId.trim().isEmpty()) {
            throw new IllegalArgumentException("Vehicle ID cannot be empty.");
        }
        if (model == null || model.trim().isEmpty()) {
            throw new IllegalArgumentException("Model cannot be empty.");
        }
        if (baseRatePerDay < 0) {
            throw new IllegalArgumentException("Rate cannot be negative.");
        }

        this.vehicleId = vehicleId.trim();
        this.model = model.trim();
        this.baseRatePerDay = baseRatePerDay;
        this.isAvailable = true;
    }

    public String getVehicleId() {
        return vehicleId;
    }

    public String getModel() {
        return model;
    }

    public double getBaseRatePerDay() {
        return baseRatePerDay;
    }

    public boolean isAvailable() {
        return isAvailable;
    }

    public void setAvailable(boolean available) {
        isAvailable = available;
    }

    public abstract double calculateRent(int days);

    @Override
    public String toString() {
        return String.format(
            "[%s] %s - Rs.%.2f/day (Available: %b)",
            vehicleId, model, baseRatePerDay, isAvailable
        );
    }
}

// =====================================================
// 2. CAR CLASS
// =====================================================
class Car extends Vehicle {
    private boolean hasAC;

    public Car(String vehicleId, String model,
               double baseRatePerDay, boolean hasAC) {
        super(vehicleId, model, baseRatePerDay);
        this.hasAC = hasAC;
    }

    @Override
    public double calculateRent(int days) {
        if (days <= 0) {
            throw new IllegalArgumentException(
                "Rental days must be greater than zero."
            );
        }

        double total = getBaseRatePerDay() * days;

        if (hasAC) {
            total += 500 * days;
        }

        return total;
    }
}

// =====================================================
// 3. CUSTOMER CLASS
// =====================================================
class Customer {
    private String customerId;
    private String name;
    private String contactNumber;

    public Customer(String customerId, String name,
                    String contactNumber) {
        if (customerId == null || customerId.trim().isEmpty()) {
            throw new IllegalArgumentException(
                "Customer ID cannot be empty."
            );
        }
        if (name == null || name.trim().isEmpty()) {
            throw new IllegalArgumentException(
                "Customer name cannot be empty."
            );
        }

        this.customerId = customerId.trim();
        this.name = name.trim();
        this.contactNumber = contactNumber;
    }

    public String getCustomerId() {
        return customerId;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return String.format(
            "ID: %s | Name: %s | Contact: %s",
            customerId, name, contactNumber
        );
    }
}

// =====================================================
// 4. RENTAL TRANSACTION CLASS
// =====================================================
class RentalTransaction {
    private Customer customer;
    private Vehicle vehicle;
    private int rentalDays;
    private double totalCost;
    private boolean isCompleted;

    public RentalTransaction(Customer customer, Vehicle vehicle,
                             int rentalDays) {
        if (customer == null || vehicle == null) {
            throw new IllegalArgumentException(
                "Customer and vehicle are required."
            );
        }
        if (rentalDays <= 0) {
            throw new IllegalArgumentException(
                "Rental days must be greater than zero."
            );
        }

        this.customer = customer;
        this.vehicle = vehicle;
        this.rentalDays = rentalDays;
        this.totalCost = vehicle.calculateRent(rentalDays);
        this.isCompleted = false;
    }

    public Vehicle getVehicle() {
        return vehicle;
    }

    public Customer getCustomer() {
        return customer;
    }

    public boolean isCompleted() {
        return isCompleted;
    }

    public void completeTransaction() {
        isCompleted = true;
    }

    public void printInvoice() {
        System.out.println("\n====== RENTAL INVOICE ======");
        System.out.println("Customer: " + customer.getName());
        System.out.println(
            "Vehicle:  " + vehicle.getModel()
            + " [" + vehicle.getVehicleId() + "]"
        );
        System.out.println("Duration: " + rentalDays + " Days");
        System.out.printf("Total Bill: Rs. %.2f%n", totalCost);
        System.out.println("=============================");
    }
}

// =====================================================
// 5. RENTAL AGENCY CLASS
// =====================================================
class RentalAgency {
    private List<Vehicle> fleet;
    private List<Customer> customers;
    private List<RentalTransaction> activeTransactions;

    public RentalAgency() {
        fleet = new ArrayList<>();
        customers = new ArrayList<>();
        activeTransactions = new ArrayList<>();
    }

    // BUG 4 FIX: Prevent duplicate vehicle IDs
    public void addVehicle(Vehicle vehicle) {
        if (vehicle == null) {
            System.out.println("[ERROR] Invalid vehicle.");
            return;
        }

        for (Vehicle v : fleet) {
            if (v.getVehicleId().equalsIgnoreCase(
                    vehicle.getVehicleId())) {
                System.out.println(
                    "[ERROR] Vehicle ID already exists: "
                    + vehicle.getVehicleId()
                );
                return;
            }
        }

        fleet.add(vehicle);
        System.out.println(
            "[SUCCESS] Vehicle added: " + vehicle.getModel()
        );
    }

    // BUG 5 FIX: Prevent duplicate customer IDs
    public void registerCustomer(Customer customer) {
        if (customer == null) {
            System.out.println("[ERROR] Invalid customer.");
            return;
        }

        for (Customer c : customers) {
            if (c.getCustomerId().equalsIgnoreCase(
                    customer.getCustomerId())) {
                System.out.println(
                    "[ERROR] Customer ID already exists: "
                    + customer.getCustomerId()
                );
                return;
            }
        }

        customers.add(customer);
        System.out.println(
            "[SUCCESS] Customer registered: " + customer.getName()
        );
    }

    // Remove customer only if no active rental exists
    public void removeCustomer(String customerId) {
        Customer targetCustomer = null;

        for (Customer c : customers) {
            if (c.getCustomerId().equalsIgnoreCase(customerId)) {
                targetCustomer = c;
                break;
            }
        }

        if (targetCustomer == null) {
            System.out.println(
                "[ERROR] Customer ID not found: " + customerId
            );
            return;
        }

        for (RentalTransaction tx : activeTransactions) {
            if (tx.getCustomer().getCustomerId()
                    .equalsIgnoreCase(customerId)
                    && !tx.isCompleted()) {
                System.out.println(
                    "[ERROR] Cannot remove "
                    + targetCustomer.getName()
                    + ". Customer has an active rental."
                );
                return;
            }
        }

        customers.remove(targetCustomer);
        System.out.println(
            "[SUCCESS] Customer removed: "
            + targetCustomer.getName()
        );
    }

    public void displayCustomers() {
        System.out.println("\n--- Registered Customers ---");

        if (customers.isEmpty()) {
            System.out.println("(No customers registered)");
            return;
        }

        for (Customer c : customers) {
            System.out.println(c);
        }
    }

    // BUGS 1, 2 and 3 FIXED:
    // Validate days, vehicle ID and registered customer
    public void rentVehicle(String vehicleId,
                            Customer customer, int days) {
        if (days <= 0) {
            System.out.println(
                "[ERROR] Rental days must be greater than zero."
            );
            return;
        }

        if (customer == null) {
            System.out.println("[ERROR] Invalid customer.");
            return;
        }

        Customer registeredCustomer = null;

        for (Customer c : customers) {
            if (c.getCustomerId().equalsIgnoreCase(
                    customer.getCustomerId())) {
                registeredCustomer = c;
                break;
            }
        }

        if (registeredCustomer == null) {
            System.out.println(
                "[ERROR] Customer is not registered."
            );
            return;
        }

        Vehicle targetVehicle = null;

        for (Vehicle v : fleet) {
            if (v.getVehicleId().equalsIgnoreCase(vehicleId)) {
                targetVehicle = v;
                break;
            }
        }

        if (targetVehicle == null) {
            System.out.println(
                "[ERROR] Vehicle ID not found: " + vehicleId
            );
            return;
        }

        if (!targetVehicle.isAvailable()) {
            System.out.println(
                "[ERROR] " + targetVehicle.getModel()
                + " is already booked."
            );
            return;
        }

        try {
            RentalTransaction tx = new RentalTransaction(
                registeredCustomer, targetVehicle, days
            );

            targetVehicle.setAvailable(false);
            activeTransactions.add(tx);
            tx.printInvoice();

        } catch (IllegalArgumentException e) {
            System.out.println("[ERROR] " + e.getMessage());
        }
    }

    // BUG 6 FIX: Report invalid or inactive vehicle returns
    public void returnVehicle(String vehicleId) {
        for (RentalTransaction tx : activeTransactions) {
            if (tx.getVehicle().getVehicleId()
                    .equalsIgnoreCase(vehicleId)
                    && !tx.isCompleted()) {

                tx.getVehicle().setAvailable(true);
                tx.completeTransaction();

                System.out.println(
                    "[SUCCESS] "
                    + tx.getVehicle().getModel()
                    + " returned successfully."
                );
                return;
            }
        }

        System.out.println(
            "[ERROR] No active rental found for vehicle ID: "
            + vehicleId
        );
    }
}

// =====================================================
// 6. MAIN CLASS
// =====================================================
public class Main {
    public static void main(String[] args) {
        RentalAgency agency = new RentalAgency();

        agency.addVehicle(
            new Car("C001", "Honda Civic", 4000, true)
        );

        System.out.println("\n>>> Registering Customers...");

        Customer customer1 = new Customer(
            "CUST01", "Ali ", "0300-1234567"
        );

        Customer customer2 = new Customer(
            "CUST02", "DinMuhammad", "03702832988"
        );

        agency.registerCustomer(customer1);
        agency.registerCustomer(customer2);
        agency.displayCustomers();

        System.out.println("\n>>> Ahmed Ali rents a car...");
        agency.rentVehicle("C001", customer1, 3);

        System.out.println("\n>>> Attempting invalid rental...");
        agency.rentVehicle("C001", customer1, -2);

        System.out.println("\n>>> Attempting unknown vehicle...");
        agency.rentVehicle("BAD-ID", customer1, 2);

        System.out.println("\n>>> Attempting unregistered customer...");
        Customer unknown = new Customer(
            "CUST99", "Unknown Customer", "0300-0000000"
        );
        agency.rentVehicle("C001", unknown, 2);

        System.out.println("\n>>> Attempting duplicate vehicle...");
        agency.addVehicle(
            new Car("C001", "Toyota Corolla", 3500, false)
        );

        System.out.println("\n>>> Attempting duplicate customer...");
        agency.registerCustomer(
            new Customer("CUST01", "Ahmed  Ali", "0342 2601215")
        );

        System.out.println("\n>>> Attempting to remove Ali...");
        agency.removeCustomer("CUST01");

        System.out.println("\n>>> Returning the car...");
        agency.returnVehicle("C001");

        System.out.println("\n>>> Attempting second return...");
        agency.returnVehicle("C001");

        System.out.println("\n>>> Removing customers...");
        agency.removeCustomer("CUST02");
        agency.removeCustomer("CUST01");

        agency.displayCustomers();
    }
}
