CREATE DATABASE shop_db;
select * from pg_database;

CREATE TABLESPACE my_tablespace LOCATION '/path/to/tablespace/directory';
select * from pg_tablespace;

CREATE ROLE analys WITH PASSWORD '123456789';
ALTER TABLESPACE my_tablespace OWNER TO analys;
ALTER TABLESPACE my_tablespace SET analys TO TABLESPACE OWNER CONNECT, CREATETABLESPACE;
GRANT SELECT ON ALL TABLES IN SCHEMA shop TO "readonly";
ALTER DEFAULT PRIVILEGES GRANT SELECT ON TABLES TO analys;

select rolname, rolcanlogin from pg_roles;

DROP SCHEMA IF EXISTS schema_shop;
CREATE SCHEMA schema_shop;


DROP TABLE IF EXISTS schema_shop.products CASCADE;
CREATE TABLE schema_shop.products(
	"id" BIGINT NOT NULL,
    "category_id" BIGINT NOT NULL,
    "name" VARCHAR(255) NOT NULL,
    "description" TEXT NOT NULL,
    "manufacturer_id" BIGINT NOT NULL,
    "supplier_id" BIGINT NOT NULL
) TABLESPACE my_tablespace;
ALTER TABLE schema_shop.products ADD PRIMARY KEY("id");
COMMENT ON COLUMN schema_shop.products.id IS 'ID продукта';
COMMENT ON COLUMN schema_shop.products.category_id IS 'ID категории продукта';
COMMENT ON COLUMN schema_shop.products.name IS 'Наименование продукта';
COMMENT ON COLUMN schema_shop.products.description IS 'Описание продукта';
COMMENT ON COLUMN schema_shop.products.manufacturer_id IS 'ID производителя';
COMMENT ON COLUMN schema_shop.products.supplier_id IS 'ID поставщика';

CREATE INDEX idx_products_name_description ON schema_shop.products(name,description);

CREATE TABLE schema_shop.categories(
	"id" BIGINT NOT NULL,
	"name" VARCHAR(255) NOT NULL
) TABLESPACE my_tablespace;
ALTER TABLE schema_shop.categories ADD PRIMARY KEY("id");
    COMMENT ON COLUMN schema_shop.categories.id IS 'ID категории';
    COMMENT ON COLUMN schema_shop.categories.name IS 'Наименование категории';

CREATE INDEX idx_categories_name ON schema_shop.categories(name);

CREATE TABLE schema_shop.manufacturers(
	"id" BIGINT NOT NULL,
	"name" VARCHAR(255) NOT NULL,
	"address" VARCHAR(255) NOT NULL,
	"phone" VARCHAR(255) NOT NULL,
	"email" VARCHAR(255) NOT NULL
) TABLESPACE my_tablespace;
	ALTER TABLE schema_shop.manufacturers ADD PRIMARY KEY("id");
COMMENT ON COLUMN schema_shop.manufacturers.id IS 'ID производителя';
COMMENT ON COLUMN schema_shop.manufacturers.name IS 'Наименование производителя';
COMMENT ON COLUMN schema_shop.manufacturers.address IS 'Адрес';
COMMENT ON COLUMN schema_shop.manufacturers.phone IS 'Телефон';
COMMENT ON COLUMN schema_shop.manufacturers.email IS 'E-mail';

CREATE TABLE schema_shop.suppliers(
	"id" BIGINT NOT NULL,
	"name" VARCHAR(255) NOT NULL,
	"address" VARCHAR(255) NOT NULL,
	"phone" VARCHAR(255) NOT NULL,
	"email" VARCHAR(255) NOT NULL
) TABLESPACE my_tablespace;
	ALTER TABLE schema_shop.suppliers ADD PRIMARY KEY("id");
COMMENT ON COLUMN schema_shop.suppliers.id IS 'ID поставщика';
COMMENT ON COLUMN schema_shop.suppliers.name IS 'Наименование поставщика';
COMMENT ON COLUMN schema_shop.suppliers.address IS 'Адрес';
COMMENT ON COLUMN schema_shop.suppliers.phone IS 'Телефон';
COMMENT ON COLUMN schema_shop.suppliers.email IS 'E-mail';

 CREATE INDEX idx_suppliers_name_address_phone_email ON schema_shop.suppliers(name,address,phone,email);

 CREATE TABLE schema_shop.purchases(
 	"id" BIGINT NOT NULL,
    "product_id" BIGINT NOT NULL,
        "customer_id" BIGINT NOT NULL,
        "purchase_date" DATE NOT NULL,
        "price_id" BIGINT NOT NULL,
        "quantity" BIGINT NOT NULL
) TABLESPACE my_tablespace;
ALTER TABLE schema_shop.purchases ADD PRIMARY KEY("id");
COMMENT ON COLUMN schema_shop.purchases.product_id IS 'ID продукта';
COMMENT ON COLUMN schema_shop.purchases.customer_id IS 'ID покупателя';
COMMENT ON COLUMN schema_shop.purchases.purchase_date IS 'Дата покупки';
COMMENT ON COLUMN schema_shop.purchases.price_id IS 'ID цены';
COMMENT ON COLUMN schema_shop.purchases.quantity IS 'Количество';

CREATE TABLE schema_shop.customers(
    "id" BIGINT NOT NULL,
    "name" VARCHAR(255) NOT NULL,
    "address" VARCHAR(255) NOT NULL,
    "phone" VARCHAR(255) NOT NULL,
    "email" VARCHAR(255) NOT NULL
) TABLESPACE my_tablespace;
ALTER TABLE schema_shop.customers ADD PRIMARY KEY("id");
COMMENT ON COLUMN schema_shop.customers.id IS 'ID Покупателя';
COMMENT ON COLUMN schema_shop.customers.name IS 'Наименование покупателя';
COMMENT ON COLUMN schema_shop.customers.address IS 'Адрес';
COMMENT ON COLUMN schema_shop.customers.phone IS 'Телефон';
COMMENT ON COLUMN schema_shop.customers.email IS 'E-Mail';

CREATE INDEX idx_customers_name_address_phone_email ON schema_shop.customers(name,address,phone,email);

CREATE TABLE schema_shop.prices(
    "id" BIGINT NOT NULL,
    "product_id" BIGINT NOT NULL,
    "price" BIGINT NOT NULL
) TABLESPACE my_tablespace;;
ALTER TABLE schema_shop.prices ADD PRIMARY KEY("id");
COMMENT ON COLUMN schema_shop.prices.id IS 'ID цены';
COMMENT ON COLUMN schema_shop.prices.product_id IS 'ID продукта';
COMMENT ON COLUMN schema_shop.prices.price IS 'Цена';

CREATE INDEX idx_prices_price ON schema_shop.prices(price);

ALTER TABLE schema_shop.purchases ADD CONSTRAINT "purchases_price_id_foreign" FOREIGN KEY("price_id") REFERENCES schema_shop.prices("id");
ALTER TABLE schema_shop.products ADD CONSTRAINT "products_supplier_id_foreign" FOREIGN KEY("supplier_id") REFERENCES schema_shop.suppliers("id");
ALTER TABLE schema_shop.purchases ADD CONSTRAINT "purchases_product_id_foreign" FOREIGN KEY("product_id") REFERENCES schema_shop.products("id");
ALTER TABLE schema_shop.purchases ADD CONSTRAINT "purchases_customer_id_foreign" FOREIGN KEY("customer_id") REFERENCES schema_shop.customers("id");
ALTER TABLE schema_shop.products ADD CONSTRAINT "products_category_id_foreign" FOREIGN KEY("category_id") REFERENCES schema_shop.categories("id");
ALTER TABLE schema_shop.products ADD CONSTRAINT "products_manufacturer_id_foreign" FOREIGN KEY("manufacturer_id") REFERENCES schema_shop.manufacturers("id");
ALTER TABLE schema_shop.prices ADD CONSTRAINT "prices_product_id_foreign" FOREIGN KEY("product_id") REFERENCES schema_shop.products("id");
