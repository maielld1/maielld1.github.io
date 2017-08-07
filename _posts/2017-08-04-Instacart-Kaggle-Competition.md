---
layout: single
title: "Instacart Market Basket Analysis \n (Kaggle Competition)"
header:
  image: /assets/images/Instacart/Instacart_kaggle.png
  caption: "Photo credit: [**Kaggle**](https://kaggle.com)"
permalink: /posts/2017/08/Instacart-Kaggle
---

**Currently in the top 8% of the public leaderboard**

I had my eye on this competition for a couple months and, after my first few weeks of some intensive learning at Metis bootcamp, I felt like I was ready to officially take it on.

For anyone who hasn't heard of Instacart:

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
  <a href="/assets/images/Instacart/cart_reorder.png"><img src="/assets/images/Instacart/cart_reorder.png"></a>
</figure>

Just as we thought! The earlier a product is added to a customer's cart, the more likely that product is to be reordered in the future.

How about looking at departments?

<figure>
  <a href="/assets/images/Instacart/department_reorder.png"><img src="/assets/images/Instacart/department_reorder.png"></a>
</figure>

This makes sense too! Produce items get reordered a lot as they expire, but personal care items don't get reordered quite as often. You don't need new toothpaste every week...unless you have a huge family.

And check out this cool visualization of the department and aisle sizes made with d3! The bigger the bubble, the more popular the department/aisle/product.

<meta charset="utf-8">
<style>

.svg {
  display: block;
  margin: auto;
  border: 1px solid gray;
}

.node {
  cursor: pointer;
}

.node:hover {
  stroke: #000;
  stroke-width: 1.5px;
}

.node--leaf {
  fill: white;
}

.label {
  font: 18px "Helvetica Neue", Helvetica, Arial, sans-serif;
  fill: black;
  text-anchor: middle;
  text-shadow: 0 1px 0 #fff, 1px 0 0 #fff, -1px 0 0 #fff, 0 -1px 0 #fff;
}

.label,
.node--root,
.node--leaf {
  pointer-events: none;
}

</style>

<svg width="960" height="960"></svg>
<script src="https://d3js.org/d3.v4.min.js"></script>
<script>

var svg = d3.select("svg"),
    margin = 20,
    diameter = +svg.attr("width"),
    g = svg.append("g").attr("transform", "translate(" + diameter / 2 + "," + diameter / 2 + ")");

var color = d3.scaleLinear()
    .domain([-1, 5])
    //.range(["hsl(152,80%,80%)", "hsl(228,30%,40%)"])
    .range(["green", "white"])
    .interpolate(d3.interpolateHcl);

var pack = d3.pack()
    .size([diameter - margin, diameter - margin])
    .padding(2);

d3.json("/assets/images/Instacart/less_food_circles.json", function(error, root) {
  if (error) throw error;

  root = d3.hierarchy(root)
      .sum(function(d) { return d.size; })
      .sort(function(a, b) { return b.value - a.value; });

  var focus = root,
      nodes = pack(root).descendants(),
      view;

  var circle = g.selectAll("circle")
    .data(nodes)
    .enter().append("circle")
      .attr("class", function(d) { return d.parent ? d.children ? "node" : "node node--leaf" : "node node--root"; })
      .style("fill", function(d) { return d.children ? color(d.depth) : null; })
      .on("click", function(d) { if (focus !== d) zoom(d), d3.event.stopPropagation(); });

  var text = g.selectAll("text")
    .data(nodes)
    .enter().append("text")
      .attr("class", "label")
      .style("fill-opacity", function(d) { return d.parent === root ? 1 : 0; })
      .style("display", function(d) { return d.parent === root ? "inline" : "none"; })
      .text(function(d) { return d.data.name; });
      //.style("font-size", function(d) { return Math.min(2 * d.r, (2 * d.r - 8) / this.getComputedTextLength() * 10) + "px"; });


  var node = g.selectAll("circle,text");

  svg
      .style("background", color(-1))
      .on("click", function() { zoom(root); });

  zoomTo([root.x, root.y, root.r * 2 + margin]);

  function zoom(d) {
    var focus0 = focus; focus = d;

    var transition = d3.transition()
        .duration(d3.event.altKey ? 7500 : 2000)
        .tween("zoom", function(d) {
          var i = d3.interpolateZoom(view, [focus.x, focus.y, focus.r * 2 + margin]);
          return function(t) { zoomTo(i(t)); };
        });

    transition.selectAll("text")
      .filter(function(d) { return d.parent === focus || this.style.display === "inline"; })
        .style("fill-opacity", function(d) { return d.parent === focus ? 1 : 0; })
        .on("start", function(d) { if (d.parent === focus) this.style.display = "inline"; })
        .on("end", function(d) { if (d.parent !== focus) this.style.display = "none"; });
  }

  function zoomTo(v) {
    var k = diameter / v[2]; view = v;
    node.attr("transform", function(d) { return "translate(" + (d.x - v[0]) * k + "," + (d.y - v[1]) * k + ")"; });
    circle.attr("r", function(d) { return d.r * k; });
  }
});

</script>

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
2. Times User Ordered the Product
3. Product Reorder Frequency (2 divided by total user orders)
4. Orders Since Previous Product Order
5. Days Since Previous Product Order
6. Streak of Product Orders (number of orders in a row)

After putting all of this together, I can confidently say I am a dataframe groupby, aggregate, and merging master.

### Modeling

Being that we are trying to predict whether a product will or will not be reordered, this makes it a binary classification problem. There are tons of algorithms that we can try, but for simplicity I'll only consider two models:

1. **Logistic Regression** (fast for our large amount of data)
2. **XGBoost** (gradient boosted trees, always works well on Kaggle Comps)

Being that this is my first official crack at a Kaggle competition, I saw this as a great opportunity to become familiar with the famed XGBoost library (thank you Tianqi Chen!).

Now having used it, I am ALL ABOUT IT.

It is important to mention that this contest ranks submissions on **F1 score** (harmonic mean between precision and recall), which makes sense in this application. Instacart doesn't want to send recommendations to people for products that they are not interested in. They also don't want to miss an opportunity to convince someone to buy a product they likely will want. We will need to tune our model to maximize F1.

### Classifications

When we run the models on our grouped data with all of our engineered features, we find that the probabilities for reorder are very low across the board. We'll need to be more lenient than requiring over 50% reorder probability to classify as a reorder.

<figure>
  <a href="/assets/images/Instacart/proba_1.png"><img src="/assets/images/Instacart/proba_1.png"></a>
</figure>

This (fake) data illustrates how difficult it will be to classify reorders. It makes sense though with the class imbalance. If we simply predicted "Not Reorder" for every product, we would be right about 85% of the time. 85% accuracy is great in some applications but not if we have 0 recall.

Let's be more lenient here and make a 20% threshold for reorders:

<figure>
  <a href="/assets/images/Instacart/proba_2.png"><img src="/assets/images/Instacart/proba_2.png"></a>
</figure>

Now that captures the right classifications!

On a larger scale with the real data, we can run our models and get our reorder probabilities for each user/product. Then we can than try a bunch of thresholds and see which one yields the best F1 score!

#### Side note: This is when I moved my data to a 64GB RAM AWS Instance, the data was too much for my measly 8GB RAM Macbook. I shouldn't complain though...I bought it off craigslist.

<figure class="half">
    <a href="/assets/images/Instacart/thres_f1.png"><img src="/assets/images/Instacart/thres_f1.png"></a>
    <a href="/assets/images/Instacart/feature_imp.png"><img src="/assets/images/Instacart/feature_imp.png"></a>
    <figcaption>Optimizing F1, and XGBoost Feature Importances</figcaption>
</figure>

Great! So we're seeing an optimal threshold set at around 0.21 or 0.22. We also see a bunch of our engineered features at the top of XGBoost's model importance list. The hard work paid off!







“The Instacart Online Grocery Shopping Dataset 2017”, Accessed from https://www.instacart.com/datasets/grocery-shopping-2017

[Kaggle Competiton Link](https://www.kaggle.com/c/instacart-market-basket-analysis)

Acknowledgments:

1. Instacart for open-sourcing their data!
2. Kaggle for hosting the competition
3. Kaggle community for sharing so many ideas on the discussion board
4. Joseph Eddy and Leang Chiang (Instagang) for collaborating on this data set at Metis
