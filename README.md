import java.util.ArrayList;
import java.util.Scanner;

// ===== ENCAPSULATION =====
class Hotel {
    private String hotelName;
    private double[] floorRates;

    public Hotel(String hotelName, double[] floorRates) {
        this.hotelName = hotelName;
        this.floorRates = floorRates;
    }

    public String getHotelName() {
        return hotelName;
    }

    public double getRateForFloor(int floor) {
        return floorRates[floor - 1];
    }

    public int getTotalFloors() {
        return floorRates.length;
    }

    public void showHotelInfo() {
        System.out.println("\n===== HOTEL INFORMATION =====");
        System.out.println("Hotel: " + hotelName);
        for (int i = 0; i < floorRates.length; i++) {
            System.out.printf("Floor %d - %.2f per day (3 rooms)%n", (i + 1), floorRates[i]);
        }
    }
}

class Room extends Hotel {
    private int floor;
    private int roomNumber;
    private double roomRate;

    public Room(String hotelName, double[] floorRates, int floor, int roomNumber) {
        super(hotelName, floorRates);
        this.floor = floor;
        this.roomNumber = roomNumber;
        this.roomRate = getRateForFloor(floor);
    }

    public int getFloor() { return floor; }
    public int getRoomNumber() { return roomNumber; }
    public double getRoomRate() { return roomRate; }
}

class Booking {
    String touristName;
    int weekday;
    Room bookedRoom;

    public Booking(String touristName, int weekday, Room bookedRoom) {
        this.touristName = touristName;
        this.weekday = weekday;
        this.bookedRoom = bookedRoom;
    }

    public double calculateTotalFee() {
        return bookedRoom.getRoomRate();
    }

    public String getWeekdayName() {
        return switch (weekday) {
            case 1 -> "Monday";
            case 2 -> "Tuesday";
            case 3 -> "Wednesday";
            case 4 -> "Thursday";
            case 5 -> "Friday";
            case 6 -> "Saturday";
            case 7 -> "Sunday";
            default -> "Unknown";
        };
    }

    public void showBookingSummary() {
        System.out.println("\n----- BOOKING SUMMARY -----");
        System.out.println("Tourist Name: " + touristName);
        System.out.println("Hotel Name: " + bookedRoom.getHotelName());
        System.out.println("Floor: " + bookedRoom.getFloor());
        System.out.println("Room Number: " + bookedRoom.getRoomNumber());
        System.out.println("Day: " + getWeekdayName());
        System.out.printf("Rate per Day: %.2f%n", bookedRoom.getRoomRate());
        System.out.printf("Total Fee: %.2f%n", calculateTotalFee());
    }

    public boolean isDuplicate(Booking other) {
        return this.bookedRoom.getFloor() == other.bookedRoom.getFloor()
                && this.bookedRoom.getRoomNumber() == other.bookedRoom.getRoomNumber()
                && this.weekday == other.weekday;
    }
}

// ===== MAIN CLASS =====
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);

        double[] hotelRates = {1500, 1600, 1700, 1800, 1900, 2000, 2100};
        Hotel hotel = new Hotel("Grand Tower Hotel", hotelRates);

        ArrayList<Booking> bookings = new ArrayList<>();
        int choice;

        do {
            System.out.println("\n===== MAIN MENU =====");
            System.out.println("[1] View Hotel Details");
            System.out.println("[2] Book a Room");
            System.out.println("[3] View Available Rooms by Day");
            System.out.println("[4] Admin Login");
            System.out.println("[5] Exit");
            System.out.print("Enter your choice: ");
            choice = in.nextInt();

            switch (choice) {

                case 1 -> hotel.showHotelInfo();

                case 2 -> {
                    in.nextLine();
                    System.out.print("\nTourist Name: ");
                    String tname = in.nextLine();

                    System.out.print("Select Floor (1–7): ");
                    int floor = in.nextInt();

                    System.out.print("Select Room Number (1–3): ");
                    int roomNum = in.nextInt();

                    System.out.println("""
Select Weekday:
1 - Monday
2 - Tuesday
3 - Wednesday
4 - Thursday
5 - Friday
6 - Saturday
7 - Sunday
""");
                    System.out.print("Enter day (1-7): ");
                    int weekday = in.nextInt();

                    Room bookedRoom = new Room(hotel.getHotelName(), hotelRates, floor, roomNum);
                    Booking newBooking = new Booking(tname, weekday, bookedRoom);

                    boolean conflict = false;
                    for (Booking b : bookings) {
                        if (newBooking.isDuplicate(b)) {
                            conflict = true;
                            break;
                        }
                    }

                    if (conflict) {
                        System.out.println("Room already booked on that day!");
                    } else {
                        bookings.add(newBooking);
                        newBooking.showBookingSummary();
                        System.out.println("Booking added successfully.");
                    }
                }

                case 3 -> {
                    System.out.println("""
Select Weekday:
1 - Monday
2 - Tuesday
3 - Wednesday
4 - Thursday
5 - Friday
6 - Saturday
7 - Sunday
""");
                    System.out.print("Enter day: ");
                    int day = in.nextInt();

                    System.out.println("\n===== AVAILABLE ROOMS FOR DAY " + day + " =====");

                    for (int floor = 1; floor <= 7; floor++) {
                        for (int room = 1; room <= 3; room++) {
                            boolean booked = false;

                            for (Booking b : bookings) {
                                if (b.bookedRoom.getFloor() == floor &&
                                        b.bookedRoom.getRoomNumber() == room &&
                                        b.weekday == day) {
                                    booked = true;
                                    break;
                                }
                            }

                            if (booked) {
                                System.out.println("Floor " + floor + " Room " + room + " : BOOKED");
                            } else {
                                System.out.println("Floor " + floor + " Room " + room + " : Available");
                            }
                        }
                    }
                }

                // ===== ADMIN LOGIN =====
                case 4 -> {
                    in.nextLine();
                    System.out.print("\nEnter Admin Username: ");
                    String user = in.nextLine();

                    System.out.print("Enter Password: ");
                    String pass = in.nextLine();

                    if (user.equals("admin") && pass.equals("admin123")) {
                        System.out.println("\nLogin successful!");

                        int adminChoice;
                        do {
                            System.out.println("\n===== ADMIN MENU =====");
                            System.out.println("[1] View All Bookings");
                            System.out.println("[2] Cancel a Booking");
                            System.out.println("[3] Logout");
                            System.out.print("Enter choice: ");
                            adminChoice = in.nextInt();

                            switch (adminChoice) {

                                case 1 -> {
                                    if (bookings.isEmpty()) {
                                        System.out.println("\nNo bookings found.");
                                    } else {
                                        System.out.println("\n===== ALL BOOKINGS =====");
                                        for (Booking b : bookings) {
                                            b.showBookingSummary();
                                        }
                                    }
                                }

                                case 2 -> {
                                    if (bookings.isEmpty()) {
                                        System.out.println("\nNo bookings to cancel.");
                                        break;
                                    }

                                    System.out.println("\nSelect booking to cancel:");
                                    for (int i = 0; i < bookings.size(); i++) {
                                        Booking b = bookings.get(i);
                                        System.out.println((i + 1) + ") " + b.touristName + " - "
                                                + "Floor " + b.bookedRoom.getFloor()
                                                + " Room " + b.bookedRoom.getRoomNumber()
                                                + " (" + b.getWeekdayName() + ")");
                                    }

                                    System.out.print("Enter number: ");
                                    int cancelIndex = in.nextInt();

                                    if (cancelIndex >= 1 && cancelIndex <= bookings.size()) {
                                        bookings.remove(cancelIndex - 1);
                                        System.out.println("Booking cancelled.");
                                    } else {
                                        System.out.println("Invalid selection.");
                                    }
                                }

                                case 3 -> System.out.println("Logging out...");

                                default -> System.out.println("Invalid choice.");
                            }

                        } while (adminChoice != 3);

                    } else {
                        System.out.println("Invalid admin credentials.");
                    }
                }

                case 5 -> System.out.println("\nThank you for using the system!");

                default -> System.out.println("Invalid choice! Try again.");
            }

        } while (choice != 5);

        in.close();
    }
}
