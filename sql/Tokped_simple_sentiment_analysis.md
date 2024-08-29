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

### 4. **Sentiment Analysist by Product Category**
   - Showing category, sentiment and product with the most review by category and sentiment.

   ```sql
   SELECT
       category,
       sentiment,
       COUNT(*) AS sentiment_count
   FROM
       reviews
   GROUP BY
       category, sentiment
   ORDER BY
       category, sentiment_count DESC;
   ```

![Screenshot 2024-08-26 213943](https://github.com/user-attachments/assets/e6edb6ad-c520-4618-85fb-945b69377aba)

### 5. **Sentiment Analysist by the Store(`shop_id)**
   - Trying to find out which stores have the most positive or negative reviews.

   ```sql
   SELECT
       shop_id,
       sentiment,
       COUNT(*) AS sentiment_count
   FROM
       reviews
   GROUP BY
       shop_id, sentiment
   ORDER BY
       shop_id, sentiment_count DESC;
   ```
![Screenshot 2024-08-26 214255](https://github.com/user-attachments/assets/7359e784-3b62-4df0-96b8-1f3ada7a6f5d)


### 6. **Producst with The Most Negative Reviews**
   - trying to find out which product have the most negative reviews.

   ```sql
   SELECT
       product_name,
       COUNT(*) AS negative_reviews
   FROM
       reviews
   WHERE
       sentiment = 'negatif'
   GROUP BY
       product_id, product_name
   ORDER BY
       negative_reviews DESC
   LIMIT 10;

   ```
![Screenshot 2024-08-26 214942](https://github.com/user-attachments/assets/4708f7ac-613a-48df-b4cf-3ab3d73fd875)


### 7. **Product Popularity by Rating Volume**

   ```sql
  SELECT
       product_name,
       SUM(sold) AS sum_sold,
       COUNT(rating) AS num_rating
   FROM
       reviews
   GROUP BY
       product_name
   ORDER BY
       num_rating DESC
   ```
 ![Screenshot 2024-08-29 203137](https://github.com/user-attachments/assets/0143fe8e-385e-402b-9175-f0da8bbca7ad)
