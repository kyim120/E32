## ✅ **Overview of OOP Concepts Used**

| Concept                  | Where It's Used                                                 |
| ------------------------ | --------------------------------------------------------------- |
| **Encapsulation**        | Product attributes are private/protected                        |
| **Inheritance**          | `Electronics`, `Clothing`, and `Grocery` inherit from `Product` |
| **Polymorphism**         | Virtual functions like `getPrice()` and `display()`             |
| **Abstraction**          | `Product` is an abstract class with pure virtual methods        |
| **Operator Overloading** | Overloaded `+` operator to add products to `ShoppingCart`       |
| **File Handling**        | Save cart contents to `cart.txt` using `ofstream`               |
| **Dynamic Memory**       | Products created using `new`, deleted in `~ShoppingCart()`      |

---

# 🧠 **FULL CODE EXPLAINED LINE BY LINE**

---

## 🔷 1. **Header Includes**

```cpp
#include <iostream>  // For input/output
#include <vector>    // For dynamic list of items
#include <fstream>   // For file saving
using namespace std; // Avoids prefixing std::
```

---

## 🔷 2. **Base Class: `Product` (Abstraction + Encapsulation)**

```cpp
class Product {
protected:
    string name;
    double basePrice;
```

* `name` and `basePrice` are **encapsulated** (not directly accessible outside).
* `protected` allows derived classes to access these members.

```cpp
public:
    Product(string n = "", double p = 0.0) : name(n), basePrice(p) {}
```

* Constructor with default parameters to initialize product values.

```cpp
    virtual double getPrice() const = 0;
    virtual void display() const = 0;
    virtual string getDetails() const = 0;
    virtual ~Product() {}
};
```

* **Pure virtual functions** make this an **abstract class** (cannot be instantiated directly).
* `getPrice()`: Must be defined by derived classes.
* `display()`: Used to print product info.
* `getDetails()`: Used to write product info to file.
* `virtual ~Product()`: Ensures proper destructor call in derived classes (**polymorphic deletion**).

---

## 🔷 3. **Derived Classes: Electronics, Clothing, Grocery**

Each class **inherits** from `Product` and overrides the **virtual functions**.

### 📱 `Electronics`:

```cpp
class Electronics : public Product {
    int warranty;
public:
    Electronics(string n, double p, int w) : Product(n, p), warranty(w) {}
```

* Adds `warranty` attribute.
* Calls base class constructor.

```cpp
    double getPrice() const override {
        return basePrice + (warranty * 10); // 10 per month
    }
```

* Adds warranty charge to base price.

```cpp
    void display() const override {
        cout << "📱 Electronics - " << name << " | Price: $" << getPrice()
             << " | Warranty: " << warranty << " months\n";
    }
```

* Custom display using overridden method.

```cpp
    string getDetails() const override {
        return "Electronics," + name + "," + to_string(getPrice()) + "," + to_string(warranty) + "\n";
    }
};
```

* Formats product info to be written to a file.

---

### 👕 `Clothing`:

```cpp
class Clothing : public Product {
    string size;
public:
    Clothing(string n, double p, string s) : Product(n, p), size(s) {}
```

* Adds size field like "S", "M", "L".

```cpp
    double getPrice() const override { return basePrice * 1.1; }
```

* Adds 10% tax.

```cpp
    void display() const override {
        cout << "👕 Clothing - " << name << " | Price: $" << getPrice()
             << " | Size: " << size << "\n";
    }

    string getDetails() const override {
        return "Clothing," + name + "," + to_string(getPrice()) + "," + size + "\n";
    }
};
```

---

### 🍎 `Grocery`:

```cpp
class Grocery : public Product {
    double weight;
public:
    Grocery(string n, double p, double w) : Product(n, p), weight(w) {}
```

* Includes weight (e.g. 5 kg).

```cpp
    double getPrice() const override { return basePrice * weight; }
```

* Total price = rate × weight.

```cpp
    void display() const override {
        cout << "🍎 Grocery - " << name << " | Price: $" << getPrice()
             << " | Weight: " << weight << " kg\n";
    }

    string getDetails() const override {
        return "Grocery," + name + "," + to_string(getPrice()) + "," + to_string(weight) + "kg\n";
    }
};
```

---

## 🔷 4. **Class: `ShoppingCart` (Operator Overloading + Aggregation)**

```cpp
class ShoppingCart {
    vector<Product*> items; // Aggregates different products
```

---

### ➕ Operator Overloading

```cpp
    void operator+(Product* p) {
        items.push_back(p);
    }
```

* Overloads `+` to add products like: `cart + new Electronics(...)`

---

### 🛒 Show Cart

```cpp
    void showCart() const {
        if (items.empty()) {
            cout << "\n🛒 Cart is empty.\n";
            return;
        }

        cout << "\n🛒 Shopping Cart Contents:\n";
        double total = 0;
        for (auto& item : items) {
            item->display();
            total += item->getPrice();
        }
        cout << "💰 Total: $" << total << "\n";
    }
```

* Displays all products using **polymorphism**.
* Calls overridden `display()` and `getPrice()`.

---

### 💾 Save to File

```cpp
    void saveToFile(string filename = "cart.txt") {
        ofstream outFile(filename);
        for (auto& item : items) {
            outFile << item->getDetails();
        }
        outFile.close();
        cout << "✅ Cart saved to file: " << filename << "\n";
    }
```

* Writes cart data to `cart.txt`.

---

### 🧹 Destructor

```cpp
    ~ShoppingCart() {
        for (auto& item : items)
            delete item;
    }
};
```

* Deletes all dynamically allocated product objects to avoid memory leaks.

---

## 🔷 5. **Main Program (User Input + Menu Loop)**

```cpp
void displayMenu() {
    cout << "\n========= 🛍 SHOPPING MENU =========\n";
    cout << "1. Add Electronics\n";
    cout << "2. Add Clothing\n";
    cout << "3. Add Grocery\n";
    cout << "4. View Cart\n";
    cout << "5. Save and Exit\n";
    cout << "====================================\n";
    cout << "Choose an option: ";
}
```

* Displays menu on screen.

---

### Main Loop:

```cpp
int main() {
    ShoppingCart cart;
    int choice;

    do {
        displayMenu();
        cin >> choice;

        if (cin.fail()) {
            cin.clear();
            cin.ignore(10000, '\n');
            cout << "❌ Invalid input. Try again.\n";
            continue;
        }
```

* Input validation ensures numeric input.

---

### Switch Statement for Menu Options:

```cpp
        string name, size;
        double price, weight;
        int warranty;

        switch (choice) {
        case 1:
            cout << "Enter name of Electronic item: ";
            cin >> ws;
            getline(cin, name);
            cout << "Enter base price: ";
            cin >> price;
            cout << "Enter warranty (months): ";
            cin >> warranty;
            cart + new Electronics(name, price, warranty);
            break;
```

* `cin >> ws; getline(...)` clears whitespace before taking full-line input.
* Dynamically allocates `Electronics` and adds to cart.

Same for Clothing and Grocery...

---

### View Cart and Exit

```cpp
        case 4:
            cart.showCart();
            break;

        case 5:
            cart.saveToFile();
            cout << "👋 Exiting... Thank you for shopping!\n";
            break;

        default:
            cout << "❌ Invalid choice. Please try again.\n";
        }

    } while (choice != 5);

    return 0;
}
```

* View cart or save and exit loop.

---

## ✅ Summary of Concepts

| Feature              | Example                                   |
| -------------------- | ----------------------------------------- |
| Abstraction          | Abstract class `Product`                  |
| Inheritance          | `Clothing`, `Electronics`, `Grocery`      |
| Polymorphism         | `getPrice()`, `display()`, `getDetails()` |
| Encapsulation        | `name`, `basePrice` hidden from outside   |
| Operator Overloading | `operator+` in `ShoppingCart`             |
| File Handling        | Save cart to file using `ofstream`        |
| Dynamic Allocation   | `new` used for creating product objects   |

---


