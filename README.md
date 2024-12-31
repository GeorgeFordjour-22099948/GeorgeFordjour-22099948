// Abstract class for Vehicle
public abstract class Vehicle {
    private String vehicleId;
    private String model;
    private double baseRentalRate;
    private boolean isAvailable;

    // Constructor with validation
    public Vehicle(String vehicleId, String model, double baseRentalRate) {
        this.vehicleId = vehicleId;
        this.model = model;
        this.baseRentalRate = baseRentalRate;
        this.isAvailable = true;
    }

    // Getters and Setters with validation
    public String getVehicleId() {
        return vehicleId;
    }

    public void setVehicleId(String vehicleId) {
        if (vehicleId != null && !vehicleId.trim().isEmpty()) {
            this.vehicleId = vehicleId;
        }
    }

    public String getModel() {
        return model;
    }

    public void setModel(String model) {
        if (model != null && !model.trim().isEmpty()) {
            this.model = model;
        }
    }

    public double getBaseRentalRate() {
        return baseRentalRate;
    }

    public void setBaseRentalRate(double baseRentalRate) {
        if (baseRentalRate > 0) {
            this.baseRentalRate = baseRentalRate;
        }
    }

    public boolean isAvailable() {
        return isAvailable;
    }

    public void setAvailable(boolean available) {
        this.isAvailable = available;
    }

    // Abstract methods to be implemented by subclasses
    public abstract double calculateRentalCost(int days);
    public abstract boolean isAvailableForRental();
}

// Rentable interface
interface Rentable {
    void rent(Customer customer, int days);
    void returnVehicle();
}

// Concrete class Car
public class Car extends Vehicle implements Rentable {
    private int numberOfDoors;

    public Car(String vehicleId, String model, double baseRentalRate, int numberOfDoors) {
        super(vehicleId, model, baseRentalRate);
        this.numberOfDoors = numberOfDoors;
    }

    @Override
    public double calculateRentalCost(int days) {
        return getBaseRentalRate() * days + (days > 7 ? 20 : 0); // Discounts for longer rentals
    }

    @Override
    public boolean isAvailableForRental() {
        return isAvailable();
    }

    public int getNumberOfDoors() {
        return numberOfDoors;
    }

    public void setNumberOfDoors(int numberOfDoors) {
        if (numberOfDoors > 0) {
            this.numberOfDoors = numberOfDoors;
        }
    }

    @Override
    public void rent(Customer customer, int days) {
        if (isAvailableForRental()) {
            setAvailable(false);  // Mark as rented
            customer.addRentalHistory(this, days);
        }
    }

    @Override
    public void returnVehicle() {
        setAvailable(true);  // Mark as available
    }
}

// Concrete class Motorcycle
public class Motorcycle extends Vehicle implements Rentable {
    private boolean hasSidecar;

    public Motorcycle(String vehicleId, String model, double baseRentalRate, boolean hasSidecar) {
        super(vehicleId, model, baseRentalRate);
        this.hasSidecar = hasSidecar;
    }

    @Override
    public double calculateRentalCost(int days) {
        return getBaseRentalRate() * days + (hasSidecar ? 15 : 0); // Additional cost for sidecar
    }

    @Override
    public boolean isAvailableForRental() {
        return isAvailable();
    }

    public boolean hasSidecar() {
        return hasSidecar;
    }

    public void setHasSidecar(boolean hasSidecar) {
        this.hasSidecar = hasSidecar;
    }

    @Override
    public void rent(Customer customer, int days) {
        if (isAvailableForRental()) {
            setAvailable(false);  // Mark as rented
            customer.addRentalHistory(this, days);
        }
    }

    @Override
    public void returnVehicle() {
        setAvailable(true);  // Mark as available
    }
}

// Concrete class Truck
public class Truck extends Vehicle implements Rentable {
    private double cargoCapacity; // in tons

    public Truck(String vehicleId, String model, double baseRentalRate, double cargoCapacity) {
        super(vehicleId, model, baseRentalRate);
        this.cargoCapacity = cargoCapacity;
    }

    @Override
    public double calculateRentalCost(int days) {
        return getBaseRentalRate() * days + (cargoCapacity > 5 ? 50 : 0); // Extra charge for larger trucks
    }

    @Override
    public boolean isAvailableForRental() {
        return isAvailable();
    }

    public double getCargoCapacity() {
        return cargoCapacity;
    }

    public void setCargoCapacity(double cargoCapacity) {
        if (cargoCapacity > 0) {
            this.cargoCapacity = cargoCapacity;
        }
    }

    @Override
    public void rent(Customer customer, int days) {
        if (isAvailableForRental()) {
            setAvailable(false);  // Mark as rented
            customer.addRentalHistory(this, days);
        }
    }

    @Override
    public void returnVehicle() {
        setAvailable(true);  // Mark as available
    }
}

// Customer Class to manage rental history
public class Customer {
    private String name;
    private List<RentalTransaction> rentalHistory = new ArrayList<>();

    public Customer(String name) {
        this.name = name;
    }

    public void addRentalHistory(Vehicle vehicle, int days) {
        rentalHistory.add(new RentalTransaction(vehicle, days));
    }

    public List<RentalTransaction> getRentalHistory() {
        return rentalHistory;
    }

    public String getName() {
        return name;
    }
}

// RentalTransaction Class
public class RentalTransaction {
    private Vehicle vehicle;
    private int rentalDays;

    public RentalTransaction(Vehicle vehicle, int rentalDays) {
        this.vehicle = vehicle;
        this.rentalDays = rentalDays;
    }

    public Vehicle getVehicle() {
        return vehicle;
    }

    public int getRentalDays() {
        return rentalDays;
    }

    public double getRentalCost() {
        return vehicle.calculateRentalCost(rentalDays);
    }
}

// RentalAgency Class to manage the rental process
public class RentalAgency {
    private List<Vehicle> fleet = new ArrayList<>();

    public static Vehicle createVehicle(String type, String vehicleId, String model, double baseRate) {
        switch (type.toLowerCase()) {
            case "car":
                return new Car(vehicleId, model, baseRate, 4);
            case "motorcycle":
                return new Motorcycle(vehicleId, model, baseRate, false);
            case "truck":
                return new Truck(vehicleId, model, baseRate, 10);
            default:
                throw new IllegalArgumentException("Unknown vehicle type: " + type);
        }
    }

    public void addVehicle(Vehicle vehicle) {
        fleet.add(vehicle);
    }

    public Vehicle getVehicleById(String vehicleId) {
        for (Vehicle vehicle : fleet) {
            if (vehicle.getVehicleId().equals(vehicleId)) {
                return vehicle;
            }
        }
        return null; // Vehicle not found
    }
}

// Main class to demonstrate the system
public class Main {
    public static void main(String[] args) {
        // Create a rental agency
        RentalAgency agency = new RentalAgency();

        // Create and add vehicles to the agency's fleet
        Vehicle car1 = RentalAgency.createVehicle("car", "V001", "Toyota Camry", 50);
        Vehicle bike1 = RentalAgency.createVehicle("motorcycle", "M001", "Harley Davidson", 30);
        Vehicle truck1 = RentalAgency.createVehicle("truck", "T001", "Ford F-150", 80);
        agency.addVehicle(car1);
        agency.addVehicle(bike1);
        agency.addVehicle(truck1);

        // Create a customer
        Customer customer1 = new Customer("John Doe");

        // Rent vehicles
        car1.rent(customer1, 5);  // Rent car for 5 days
        bike1.rent(customer1, 3);  // Rent motorcycle for 3 days

        // Print rental history and costs
        System.out.println("Rental History for " + customer1.getName() + ":");
        for (RentalTransaction transaction : customer1.getRentalHistory()) {
            System.out.println("Vehicle: " + transaction.getVehicle().getModel() +
                               ", Days: " + transaction.getRentalDays() +
                               ", Cost: $" + transaction.getRentalCost());
        }

        // Return vehicles
        car1.returnVehicle();
        bike1.returnVehicle();
    }
}
- 👋 Hi, I’m @GeorgeFordjour-22099948
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
GeorgeFordjour-22099948/GeorgeFordjour-22099948 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
