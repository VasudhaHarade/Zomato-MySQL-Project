# Zomato-MySQL-Project
-- Step 1: Database Schema Design
CREATE DATABASE zomato;
USE zomato;

-- Step 2: Creating Tables

-- 1. Users Table
CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    phone VARCHAR(15),
    address TEXT
);

-- 2. Restaurants Table
CREATE TABLE Restaurants (
    restaurant_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    location VARCHAR(100),
    rating FLOAT CHECK (rating >= 0 AND rating <= 5),
    cuisine VARCHAR(50),
    contact VARCHAR(15)
);

-- 3. Menu_Items Table
CREATE TABLE Menu_Items (
    item_id INT PRIMARY KEY AUTO_INCREMENT,
    restaurant_id INT,
    item_name VARCHAR(100) NOT NULL,
    price DECIMAL(8, 2) NOT NULL,
    description TEXT,
    FOREIGN KEY (restaurant_id) REFERENCES Restaurants(restaurant_id) ON DELETE CASCADE
);

-- 4. Orders Table
CREATE TABLE Orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    restaurant_id INT,
    order_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    status ENUM('Pending', 'Completed', 'Cancelled') DEFAULT 'Pending',
    total_price DECIMAL(8, 2),
    FOREIGN KEY (user_id) REFERENCES Users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (restaurant_id) REFERENCES Restaurants(restaurant_id) ON DELETE CASCADE
);

-- 5. Order_Items Table
CREATE TABLE Order_Items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT,
    item_id INT,
    quantity INT DEFAULT 1,
    price DECIMAL(8, 2),
    FOREIGN KEY (order_id) REFERENCES Orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (item_id) REFERENCES Menu_Items(item_id) ON DELETE CASCADE
);

-- 6. Reviews Table
CREATE TABLE Reviews (
    review_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    restaurant_id INT,
    rating INT CHECK (rating >= 0 AND rating <= 5),
    comments TEXT,
    review_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (restaurant_id) REFERENCES Restaurants(restaurant_id) ON DELETE CASCADE
);

-- Step 3: Inserting Sample Data

-- Users Table
INSERT INTO Users (username, email, phone, address) VALUES 
('Vasudha', 'vasudha@example.com', '1234512345', '101 Tech St'),
('Neha', 'neha@example.com', '5432154321', '202 Tech Ave'),
('Mahi', 'mahi@example.com', '9876598765', '303 Developer Rd'),
('Jeeva', 'jeeva@example.com', '6543265432', '404 Code Ln'),
('Sahana', 'sahana@example.com', '3214321432', '505 Debug Blvd');

-- Restaurants Table
INSERT INTO Restaurants (name, location, rating, cuisine, contact) VALUES 
('Pizza Palace', 'Downtown', 4.5, 'Italian', '1112223333'),
('Sushi World', 'Uptown', 4.8, 'Japanese', '2223334444'),
('Burger Hub', 'Midtown', 4.2, 'American', '3334445555'),
('Curry House', 'Oldtown', 4.6, 'Indian', '4445556666'),
('Taco Land', 'Southside', 4.3, 'Mexican', '5556667777');

-- Menu_Items Table
INSERT INTO Menu_Items (restaurant_id, item_name, price, description) VALUES 
(1, 'Margherita Pizza', 9.99, 'Classic cheese and tomato pizza'),
(1, 'Pepperoni Pizza', 12.99, 'Pepperoni pizza with mozzarella'),
(2, 'Salmon Sushi', 15.99, 'Fresh salmon nigiri sushi'),
(2, 'Tuna Sushi', 14.99, 'Fresh tuna nigiri sushi'),
(3, 'Cheeseburger', 8.99, 'Juicy beef burger with cheese');

-- Orders Table
INSERT INTO Orders (user_id, restaurant_id, total_price, status) VALUES 
(1, 1, 22.98, 'Completed'),
(2, 2, 30.98, 'Pending'),
(3, 3, 8.99, 'Completed'),
(4, 4, 50.00, 'Pending'),
(5, 5, 15.00, 'Cancelled');

-- Order_Items Table
INSERT INTO Order_Items (order_id, item_id, quantity, price) VALUES 
(1, 1, 1, 9.99),
(1, 2, 1, 12.99),
(2, 3, 2, 15.99),
(3, 5, 1, 8.99),
(4, 4, 3, 15.00);

-- Reviews Table
INSERT INTO Reviews (user_id, restaurant_id, rating, comments) VALUES 
(1, 1, 5, 'Amazing pizza! Highly recommend.'),
(2, 2, 4, 'Great sushi, but a bit pricey.'),
(3, 3, 3, 'Burger was average.'),
(4, 4, 5, 'Fantastic curry!'),
(5, 5, 4, 'Loved the tacos!');

-- Step 4: Writing Queries

-- 1. Get all restaurants and their average rating
SELECT Restaurants.name, Restaurants.location, AVG(Reviews.rating) AS average_rating
FROM Restaurants
JOIN Reviews ON Restaurants.restaurant_id = Reviews.restaurant_id
GROUP BY Restaurants.restaurant_id;

-- 2. Find all menu items of a restaurant
SELECT item_name, price, description 
FROM Menu_Items 
WHERE restaurant_id = 1;

-- 3. Retrieve all orders by a user
SELECT Orders.order_id, Restaurants.name AS restaurant_name, Orders.total_price, Orders.status
FROM Orders
JOIN Restaurants ON Orders.restaurant_id = Restaurants.restaurant_id
WHERE Orders.user_id = 1;

-- 4. Display all reviews for a restaurant
SELECT Users.username, Reviews.rating, Reviews.comments
FROM Reviews
JOIN Users ON Reviews.user_id = Users.user_id
WHERE restaurant_id = 2;

-- 5. Calculate total earnings for each restaurant
SELECT Restaurants.name, SUM(Orders.total_price) AS total_earnings
FROM Orders
JOIN Restaurants ON Orders.restaurant_id = Restaurants.restaurant_id
WHERE Orders.status = 'Completed'
GROUP BY Restaurants.restaurant_id;
