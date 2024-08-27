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
   - Some values in sold are not well-formatted; they include blank entries, the text 'broken', and values that use 'rb' to denote thousands.
   - With excel I normalize it using countif(product_id) and substitute() times 1000 for the 'rb'.
     
### 3. **Klasifikasi Sentimen Berdasarkan Rating**
   - Kita bisa menggunakan kolom `rating` untuk mengklasifikasikan sentimen secara sederhana:
     - **Positif:** Rating 4-5.
     - **Netral:** Rating 3.
     - **Negatif:** Rating 1-2.

   Berikut contoh query SQL untuk mengklasifikasikan sentimen berdasarkan rating:

   ```sql
   ALTER TABLE reviews
   ADD COLUMN sentiment text;

   UPDATE reviews
   SET sentiment = CASE
       WHEN rating >= 4 THEN 'positif'
       WHEN rating = 3 THEN 'netral'
       WHEN rating <= 2 THEN 'negatif'
   END;
