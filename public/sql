-- Database Design for TechInventory
-- Based on the current application requirements

-- 1. Users Table (Usuarios)
-- Stores user authentication and profile information
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    role VARCHAR(20) NOT NULL DEFAULT 'employee', -- 'admin' or 'employee'
    password_hash VARCHAR(255) NOT NULL, 
    avatar_url TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login TIMESTAMP
);

-- Seed Data (Users)
INSERT INTO users (name, email, role, password_hash) VALUES 
('Administrador', 'admin@techinventory.com', 'admin', 'hashed_secret'),
('Empleado 1', 'emp1@techinventory.com', 'employee', 'hashed_secret'),
('Empleado 2', 'emp2@techinventory.com', 'employee', 'hashed_secret');

-- 2. Categories Enum (Optional, or could be a lookup table)
-- Standardizing the categories used in the dropdown
-- Categories: 'Celulares', 'Pantallas', 'Baterías', 'Cargadores', 'Fundas', 'Vidrios', 'Repuestos'

-- 3. Products Table (Productos)
-- Stores the inventory items
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(150) NOT NULL,
    brand VARCHAR(50) NOT NULL,      -- e.g., Samsung, Apple, Xiaomi
    model VARCHAR(100),              -- e.g., S23 Ultra
    category VARCHAR(50) NOT NULL,   -- Indexed for faster filtering/grouping
    price DECIMAL(10, 2) NOT NULL DEFAULT 0.00,
    stock INTEGER NOT NULL DEFAULT 0,
    image_url TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Index for the "Stagnant Inventory" and "Dashboard Charts" queries
CREATE INDEX idx_products_category ON products(category);
CREATE INDEX idx_products_stock ON products(stock); -- For identifying low stock

-- 4. Transactions Table (Movimientos)
-- Records all stock changes (Sales, Restocks, etc.)
CREATE TABLE transactions (
    id SERIAL PRIMARY KEY,
    product_id INTEGER REFERENCES products(id) ON DELETE SET NULL, -- Proper linking!
    user_id INTEGER REFERENCES users(id), -- Who performed the action
    type VARCHAR(20) NOT NULL, -- 'Venta', 'Reposición', 'Entrada', 'Eliminado'
    description TEXT,          -- Human readable description
    quantity INTEGER NOT NULL,
    val_unit DECIMAL(10, 2),   -- Price at the moment of transaction
    total_value DECIMAL(10, 2), -- quantity * val_unit
    date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Index for the "Sales of the Day" and "History" queries
CREATE INDEX idx_transactions_date ON transactions(date);
CREATE INDEX idx_transactions_type ON transactions(type);
CREATE INDEX idx_transactions_product_id ON transactions(product_id);

-- Example Queries for your Features:

-- A. "Sales of the Day" (Ventas del Día)
-- SELECT SUM(total_value) 
-- FROM transactions 
-- WHERE type = 'Venta' 
-- AND date >= CURRENT_DATE;

-- B. "Stagnant Inventory" (Inventario Estancado)
-- Products (excluding 'Repuestos') with no 'Venta' transaction in last 3 weeks
-- SELECT p.* 
-- FROM products p
-- WHERE p.category != 'Repuestos'
-- AND NOT EXISTS (
--     SELECT 1 FROM transactions t 
--     WHERE t.product_id = p.id 
--     AND t.type = 'Venta' 
--     AND t.date >= NOW() - INTERVAL '21 days'
-- );
