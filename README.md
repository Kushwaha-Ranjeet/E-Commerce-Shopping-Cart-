# E-Commerce-Shopping-Cart-
build a mini shopping cart system for an e-commerce platform (like Amazon/Myntra). The cart should allow customers to add products with their name, price, and quantity.


import java.util.*;

// ---------------- Product Class (Encapsulation) ----------------
class Product {
    private String name;
    private double price;
    private int quantity;

    public Product(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    public String getName() { return name; }
    public double getPrice() { return price; }
    public int getQuantity() { return quantity; }

    public double getTotalPrice() {
        return price * quantity;
    }
}

// ---------------- Discount Polymorphism ----------------
abstract class Discount {
    abstract double applyDiscount(double total, List<Product> cart);
}

class FestiveDiscount extends Discount {
    @Override
    double applyDiscount(double total, List<Product> cart) {
        return total * 0.90; // 10% off
    }
}

class BulkDiscount extends Discount {
    @Override
    double applyDiscount(double total, List<Product> cart) {
        boolean hasBulk = false;
        for(Product p : cart) {
            if(p.getQuantity() > 5) {
                hasBulk = true; break;
            }
        }
        return hasBulk ? total * 0.80 : total;
    }
}

// ---------------- Payment Interface ----------------
interface Payment {
    void pay(double amount);
}

class CardPayment implements Payment {
    public void pay(double amount) {
        System.out.println("Payment of Rs." + amount + " done using Card.");
    }
}

// ---------------- Main Shopping Cart ----------------
public class ShoppingCart {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        // Step 1: Read input
        int n = sc.nextInt();
        sc.nextLine();

        List<Product> cart = new ArrayList<>();

        for(int i = 0; i < n; i++) {
            String input = sc.nextLine();
            String[] parts = input.split(" ");
            String name = parts[0];
            double price = Double.parseDouble(parts[1]);
            int qty = Integer.parseInt(parts[2]);
            cart.add(new Product(name, price, qty));
        }

        String discountType = sc.nextLine().trim().toLowerCase();

        // Step 2: Calculate total
        double total = 0;
        for(Product p : cart) {
            total += p.getTotalPrice();
        }

        // Step 3: Apply discount (Polymorphism)
        Discount discount;
        if(discountType.equals("festive")) {
            discount = new FestiveDiscount();
        } else {
            discount = new BulkDiscount();
        }

        double discountedTotal = discount.applyDiscount(total, cart);

        // Step 4: Print products
        for(Product p : cart) {
            System.out.println("Product: " + p.getName() +
                               ", Price: " + p.getPrice() +
                               ", Quantity: " + p.getQuantity());
        }

        // Step 5: Payment
        Payment payment = new CardPayment();
        System.out.println("Total Amount Payable: " + discountedTotal);
        payment.pay(discountedTotal);

        sc.close();
    }
}
