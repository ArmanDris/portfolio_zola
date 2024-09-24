+++
authors = ["Arman Drismir"]
title = "Default Detector"
description = "A machine learning model I deployed to a web app"
date = 2024-06-01
[taxonomies]
tags = ["Python", "CatBoost", "Machine Learning", "SciKit-Learn" , "Svelte Kit"]
[extra]
+++

I built a svelte/flask app around the ML model I made for a CPSC 330 class project.

<div>
   <h2>Dataset &amp; Metrics</h2>
   <p>This dataset is imbalanced, 22% of clients default while 78% do not. For
      this reason F1 score and Recall are the metrics we want to pay a lot of
      attention to. F1 score will give us a good indication of overall
      performance and Recall will tell us how well our model preforms when it
      sees the clients who do default.
   </p>
   <p>The Kaggle page has descriptions for what values in the columns
      represent. <a href="https://www.kaggle.com/datasets/uciml/default-of-credit-card-clients-dataset" target="_blank" class="underline">Link to dataset.</a>
   </p>
   <h2>Best Model</h2>
   <p>I was able to get a test accuracy of 82% using CatBoost with
      hyperparameters I found using RandomizedSearch. The results were less
      than I expected since a dummy classifier scores 78%. Running
      RandomizedSearch on a more powerful computer would have probably allowed
      me to squeeze a few more points of accuracy. The overall f1, recall, and
      precision scores are good but when looking specifically at the default
      class they are very bad. It would be interesting to train a new model
      that sacrifices accuracy for recall in the DEFAULT class.
   </p>
   <table>
      <tbody>
         <tr>
            <th></th>
            <th>precision</th>
            <th>recall</th>
            <th>f1-score</th>
         </tr>
         <tr>
            <td>PAID</td>
            <td>0.84</td>
            <td>0.95</td>
            <td>0.89</td>
         </tr>
         <tr>
            <td>DEFAULT</td>
            <td>0.68</td>
            <td>0.37</td>
            <td>0.48</td>
         </tr>
         <tr>
            <td>average</td>
            <td>0.81</td>
            <td>0.82</td>
            <td>0.80</td>
         </tr>
      </tbody>
   </table>
   <h2>All Models</h2>
   <h3>Linear Regression</h3>
   <p>Train score: 0.810524, improved to 0.8106 with HPO <span style="color: #859900;">(+0.000076)</span>.</p>
   <h3>SVC</h3>
   <p>Train score: 0.8177, improved to 0.8183 with HPO <span style="color: #859900;">(+0.0006)</span></p>
   <h3>Gradient Boosted Trees</h3>
   <p>Train score: 0.8190, decreased to 0.8189 with HPO <span style="color: #dc322f">(-0.0001)</span></p>
   <h3>CatBoost</h3>
   <p>Train score: 0.8175, improved to 0.8214 with HPO <span style="color: #859900;" >(+0.0039)</span></p>
</div>
