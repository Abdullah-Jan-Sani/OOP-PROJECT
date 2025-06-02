
# OOP-PROJECT

https://github.com/Abdullah-Jan-Sani
#include <iostream>
#include <vector>
#include <string>
#include <fstream>
#include <sstream>
#include <iomanip>
#include <cstdlib> // Include for atoi and atof

using namespace std;

// Base Item class
class Item {
protected:
    int id;
    string name;

public:
    Item() : id(0), name("") {}
    Item(int pid, const string& pname) : id(pid), name(pname) {}

    int getId() const { return id; }
    string getName() const { return name; }
};

// Product class inherits from Item, adds price
class Product : public Item {
private:
    double price;

public:
    Product() : price(0.0) {}
    Product(int pid, const string& pname, double pprice)
        : Item(pid, pname), price(pprice) {}

    double getPrice() const { return price; }

    void display() const {
        cout << setw(3) << id << " | "
             << setw(15) << name << " | $"
             << fixed << setprecision(2) << price << endl;
    }

    // Serialize to CSV line
    string toCSV() const {
        ostringstream oss;
        oss << id << "," << name << "," << price;
        return oss.str();
    }

    // Deserialize from CSV line
    static Product fromCSV(const string& csvLine) {
        istringstream iss(csvLine);
        string token;
        int pid = 0;
        string pname = "";
        double pprice = 0.0;

        getline(iss, token, ',');
        if (!token.empty()) pid = atoi(token.c_str());
        getline(iss, pname, ',');
        getline(iss, token, ',');
        if (!token.empty()) pprice = atof(token.c_str());

        return Product(pid, pname, pprice);
    }
};

// CartItem class to associate product and quantity
class CartItem {
private:
    Product product;
    int quantity;

public:
    CartItem(const Product& p, int qty) : product(p), quantity(qty) {}

    int getProductId() const { return product.getId(); }
    string getProductName() const { return product.getName(); }
    double getProductPrice() const { return product.getPrice(); }
    int getQuantity() const { return quantity; }
    double getTotalPrice() const { return product.getPrice() * quantity; }

    void increaseQuantity(int qty) { quantity += qty; }
    void display() const {
        cout << setw(15) << product.getName()
             << " x " << setw(3) << quantity
             << " = $" << fixed << setprecision(2) << getTotalPrice() << endl;
    }
};

// ShoppingCart class to manage CartItems
class ShoppingCart {
private:
    vector<CartItem> items;

public:
    void addItem(const Product& product, int quantity) {
        for (size_t i = 0; i < items.size(); ++i) {
            if (items[i].getProductId() == product.getId()) {
                items[i].increaseQuantity(quantity);
                cout << quantity << " more of " << product.getName() << " added to the cart.\n";
                return;
            }
        }
        items.push_back(CartItem(product, quantity));
        cout << quantity << " " << product.getName() << " added to the cart.\n";
    }

    void removeItem(int productId) {
        for (size_t i = 0; i < items.size(); ++i) {
            if (items[i].getProductId() == productId) {
                cout << items[i].getProductName() << " removed from the cart.\n";
                items.erase(items.begin() + i);
                return;
            }
        }
        cout << "Product not found in the cart.\n";
    }

    void displayCart() const {
        if (items.empty()) {
            cout << "Your cart is empty.\n";
            return;
        }
        cout << "Your Shopping Cart:\n";
        cout << "---------------------\n";
        for (size_t i = 0; i < items.size(); ++i) {
            items[i].display();
        }
        cout << "---------------------\n";
        cout << "Total: $" << fixed << setprecision(2) << getTotalPrice() << endl;
    }

    double getTotalPrice() const {
        double total = 0;
        for (size_t i = 0; i < items.size(); ++i) {
            total += items[i].getTotalPrice();
        }
        return total;
    }

    bool isEmpty() const {
        return items.empty();
    }

    void clearCart() {
        items.clear();
    }
};

// Load products from file; if file missing or error, fallback to default products
void loadProducts(const string& filename, vector<Product>& products) {
    ifstream file(filename.c_str());
    if (!file) {
        // File not found - default products
        cout << "Product file not found. Loading default products...\n";
        products.push_back(Product(1, "Apple", 0.99));
        products.push_back(Product(2, "Banana", 0.59));
        products.push_back(Product(3, "Orange", 1.29));
        products.push_back(Product(4, "Milk", 2.49));
        products.push_back(Product(5, "Bread", 1.99));
        return;
    }

    string line;
    while (getline(file, line)) {
        if(line.empty()) continue;
        Product p = Product::fromCSV(line);
        products.push_back(p);
    }
    file.close();
}

// Save products to file
void saveProducts(const string& filename, const vector<Product>& products) {
    ofstream file(filename.c_str());
    if (!file) {
        cout << "Error: Unable to save products to file.\n";
        return;
    }
    for (size_t i = 0; i < products.size(); ++i) {
        file << products[i].toCSV() << "\n";
    }
    file.close();
}

// Display products
void displayProducts(const vector<Product>& products) {
    cout << "Available Products:\n";
    cout << " ID | Product         | Price\n";
    cout << "-------------------------------\n";
    for (size_t i = 0; i < products.size(); ++i) {
        products[i].display();
    }
    cout << "-------------------------------\n";
}

int main() {
    vector<Product> products;
    const string productFile = "products.txt";

    loadProducts(productFile, products);

    ShoppingCart cart;

    int choice;
    do {
        cout << "\n====== Online Shopping Cart ======\n";
        cout << "1. Show Products\n";
        cout << "2. Add Product to Cart\n";
        cout << "3. Remove Product from Cart\n";
        cout << "4. View Cart\n";
        cout << "5. Checkout\n";
        cout << "0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
        case 1:
            displayProducts(products);
            break;
        case 2: {
            int pid, qty;
            cout << "Enter Product ID to add: ";
            cin >> pid;
            cout << "Enter quantity: ";
            cin >> qty;

            bool found = false;
            for (size_t i = 0; i < products.size(); ++i) {
                if (products[i].getId() == pid) {
                    cart.addItem(products[i], qty);
                    found = true;
                    break;
                }
            }
            if (!found) {
                cout << "Invalid Product ID.\n";
            }
            break;
        }
        case 3: {
            int pid;
            cout << "Enter Product ID to remove: ";
            cin >> pid;
            cart.removeItem(pid);
            break;
        }
        case 4:
            cart.displayCart();
            break;
        case 5:
            if (cart.isEmpty()) {
                cout << "Your cart is empty. Add products before checkout.\n";
            } else {
                cout << "Checking out...\n";
                cart.displayCart();
                cout << "Thank you for your purchase!\n";
                cart.clearCart();
            }
            break;
        case 0:
            cout << "Exiting application. Saving products...\n";
            saveProducts(productFile, products);
            cout << "Goodbye!\n";
            break;
        default:
            cout << "Invalid choice. Try again.\n";
            break;
        }
    } while (choice != 0);

    return 0;
}
