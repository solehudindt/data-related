### 1. **Understanding the Data Structure**
   - **Dataset from [Tokopedia Product Reviews](https://www.kaggle.com/datasets/farhan999/tokopedia-product-reviews) by Farhan
   - **Available Columns:**
     - `nom`: number.
     - `text`: Text review.
     - `rating`: User rating from 1-5.
     - `category`: Product Category.
     - `product_name`: Product Name.
     - `product_id`: Product ID.
     - `sold`: Number of Product Sold.
     - `shop_id`: Shop ID of the Product.
     - `product_url`: URL Product.

### 2. **A bit of Normalization before goes to PostgreSQL**
   - Some values in `sold are not well-formatted; they include blank entries, the text 'broken', and values that use 'rb' to denote thousands.
   - With excel I normalize it using countif(`product_id) and substitute() times 1000 for the 'rb'.
     
### 3. **Clasifying Sentiment based on Rating**
   - Since it's a based on out of 5 rating, the clasification of the sentiment can be set up as follow :
     - **Positif:** Rating 4-5.
     - **Netral:** Rating 3.
     - **Negatif:** Rating 1-2.

   when apply it using the query, it will be look like this :

   ```sql
   ALTER TABLE reviews
   ADD COLUMN sentiment text;

   UPDATE reviews
   SET sentiment = CASE
       WHEN rating >= 4 THEN 'positif'
       WHEN rating = 3 THEN 'netral'
       WHEN rating <= 2 THEN 'negatif'
   END;
   ```

### 4. **Menganalisis Sentimen Berdasarkan Kategori Produk**
   - Anda juga bisa melihat bagaimana sentimen berbeda berdasarkan kategori produk.

   ```sql
   SELECT
       category,
       sentiment,
       COUNT(*) AS sentiment_count
   FROM
       your_table_name
   GROUP BY
       category, sentiment
   ORDER BY
       category, sentiment_count DESC;
   ```

![Screenshot 2024-08-26 213943](https://github.com/user-attachments/assets/e6edb6ad-c520-4618-85fb-945b69377aba)

### 5. **Menganalisis Sentimen Berdasarkan Toko (Shop ID)**
   - Mengetahui toko mana yang mendapatkan ulasan positif atau negatif terbanyak juga bisa memberikan insight penting.

   ```sql
   SELECT
       shop_id,
       sentiment,
       COUNT(*) AS sentiment_count
   FROM
       your_table_name
   GROUP BY
       shop_id, sentiment
   ORDER BY
       shop_id, sentiment_count DESC;
   ```

### 7. **Mendapatkan Produk dengan Sentimen Negatif Tertinggi**
   - Query ini bisa membantu Anda menemukan produk yang paling sering mendapatkan ulasan negatif.

   ```sql
   SELECT
       product_id,
       product_name,
       COUNT(*) AS negative_reviews
   FROM
       your_table_name
   WHERE
       sentiment = 'negatif'
   GROUP BY
       product_id, product_name
   ORDER BY
       negative_reviews DESC
   LIMIT 10;

   ```
