---
layout: single
title: Instacart Market Basket Analysis \n (Kaggle Competition)
header:
  image: /assets/images/Instacart/Instacart_kaggle.png
  caption: "Photo credit: [**Kaggle**](https://kaggle.com)"
permalink: /posts/2017/08/Instacart-Kaggle
---

**Currently in the top 8% of the public leaderboard**

I had my eye on this competition for a couple months and, after my first few weeks of some intensive learning at Metis bootcamp, I felt like I was ready to officially take it on.

For anyone who hasn't head of Instacart:

"Whether you shop from meticulously planned grocery lists or let whimsy guide your grazing, our unique food rituals define who we are. Instacart, a grocery ordering and delivery app, aims to make it easy to fill your refrigerator and pantry with your personal favorites and staples when you need them. After selecting products through the Instacart app, personal shoppers review your order and do the in-store shopping and delivery for you."

As someone who dreads grocery shopping, it sounds like I am their target customer.

Well, I would be if I knew how to make any food other than Digiorno pizza or Eggo waffles...I should probably learn how to cook...

But I digress

Instacart's Data Science team currently uses transactional data to develop models that predict which products a user will buy again, try for the first time, or add to their cart next during a session. These models have a huge impact on optimizing the user experience as they can recommend products based on a user's past behavior.

{% raw %}<img src="/assets/images/Instacart/app_pic.png" alt="" class="full">{% endraw %}

### The Competition

Kaggle opened this competition a few months ago to see who could best use Instacart's open sourced data to predict what products people will repurchase in their next order. The data includes a sample of 3 Million Orders from over 200,000 customers. Each customer has made anywhere from 4 to 100 orders. Each order includes information of the hour of day, day of week, days between previous orders, etc.

### Data Pre-Processing

As the data was split into separate csv files (the users and orders, products in each orders, department names, etc.), I needed to group everything together so that I could start to extract meaningful features. Helpful features in modeling user behavior will be anything that can quantify user purchasing tendencies based on their history. Let's look at the data to see what patterns we can identify that might be useful as features for our model.

### EDA / Data Viz

Thinking about how people shop in general, it makes sense that items they add to their cart first are probably a priority to them. These are probably items that they order all of the time.

<figure>
  <a href="/assets/images/Instacart/cart_order.png"><img src="/assets/images/Instacart/cart_order.png"></a>
</figure>

Just as we thought! The earlier a product is added to a customer's cart, the more likely that product is to be reordered in the future.

How about looking at departments?

<figure>
  <a href="/assets/images/Instacart/department_reorder.png"><img src="/assets/images/Instacart/department_reorder.png"></a>
</figure>

This makes sense too! Produce items get reordered a lot as they expire, but personal care items don't get reordered quite as often. You don't need new toothpaste every week...unless you have a huge family.

I won't add more feature charts here to keep from chart overload, but there is also a clear tendency for more items being bought earlier in the week as well as in the early morning or after work hours.

### Feature Engineering!

With our EDA in mind, we can start to come up with some informative features. Here are some of the ones I found success with:

**User Specific:**

1. Average Order Size
2. Average Number of Reorders per Cart
3. Average Days Between Orders
4. Average Order Hour of Day
5. Average Oder Day of Week

**Product Specific (generalized over all users):**

1. Total Orders of Product
2. Times Product was Reordered
3. Product Reorder Probability (2 divided by 1)
4. Department (categorical)
5. Aisle (categorical)

**User/Product Relationship:**

1. Average Cart Order
2. # Times User Ordered the Product
3. Product Reorder Frequency (2 divided by total user orders)
4. # Orders Since Previous Product Order
5. Days Since Previous Product Order
6. Streak of Product Orders (number of orders in a row)



“The Instacart Online Grocery Shopping Dataset 2017”, Accessed from https://www.instacart.com/datasets/grocery-shopping-2017

[Kaggle Competiton Link](https://www.kaggle.com/c/instacart-market-basket-analysis)

Some Thank Yous:

1. Instacart for open-sourcing their data!
2. Kaggle for hosting the competition
3. Kaggle community for sharing so many ideas on the discussion board
4. Joseph Eddy and Leang Chiang (Instagang) for collaborating on this data set at Metis
