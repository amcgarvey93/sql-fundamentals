
## Analyzing CIA Factbook Data Using SQL - Dataquest SQL Project

Aaron McGarvey  
February 20, 2022

##### This goal of this project is to use SQL to analyze the CIA Factbook Database. The code is wirtten on jupyter notebook on the Dataquest platform.


**Step 1)** Connect to the database


```python
%%capture
%load_ext sql
%sql sqlite:///factbook.db
```




    'Connected: None@factbook.db'



**Step 2)** We'll be looking at the facts table. Let's take a look at the variables we will be working with.


```python
%%sql
SELECT *
  FROM facts
 LIMIT 5;
```

    Done.





<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>1</td>
        <td>af</td>
        <td>Afghanistan</td>
        <td>652230</td>
        <td>652230</td>
        <td>0</td>
        <td>32564342</td>
        <td>2.32</td>
        <td>38.57</td>
        <td>13.89</td>
        <td>1.51</td>
    </tr>
    <tr>
        <td>2</td>
        <td>al</td>
        <td>Albania</td>
        <td>28748</td>
        <td>27398</td>
        <td>1350</td>
        <td>3029278</td>
        <td>0.3</td>
        <td>12.92</td>
        <td>6.58</td>
        <td>3.3</td>
    </tr>
    <tr>
        <td>3</td>
        <td>ag</td>
        <td>Algeria</td>
        <td>2381741</td>
        <td>2381741</td>
        <td>0</td>
        <td>39542166</td>
        <td>1.84</td>
        <td>23.67</td>
        <td>4.31</td>
        <td>0.92</td>
    </tr>
    <tr>
        <td>4</td>
        <td>an</td>
        <td>Andorra</td>
        <td>468</td>
        <td>468</td>
        <td>0</td>
        <td>85580</td>
        <td>0.12</td>
        <td>8.13</td>
        <td>6.96</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>5</td>
        <td>ao</td>
        <td>Angola</td>
        <td>1246700</td>
        <td>1246700</td>
        <td>0</td>
        <td>19625353</td>
        <td>2.78</td>
        <td>38.78</td>
        <td>11.49</td>
        <td>0.46</td>
    </tr>
</table>



**Here are the descriptions for some of the columns:**

**name** — the name of the country.  
**area**— the country's total area (both land and water).  
**area_land** — the country's land area in square kilometers.  
**area_water** — the country's waterarea in square kilometers.  
**population** — the country's population.  
**population_growth**— the country's population growth as a percentage.  
**birth_rate** — the country's birth rate, or the number of births per year per 1,000 people.  
**death_rate** — the country's death rate, or the number of death per year per 1,000 people.  

**Step 2)** Let's start diving into population and get some summary statistics!


```python
%%sql
SELECT MIN(population) AS min_pop,
        MAX(population) AS max_pop, 
        MIN(population_growth) AS min_pop_growth,
        MAX(population_growth) AS max_pop_growth
    FROM facts;
        
```

    Done.





<table>
    <tr>
        <th>min_pop</th>
        <th>max_pop</th>
        <th>min_pop_growth</th>
        <th>max_pop_growth</th>
    </tr>
    <tr>
        <td>0</td>
        <td>7256490011</td>
        <td>0.0</td>
        <td>4.02</td>
    </tr>
</table>



Interesting! We have a country with a poplation of **0** and one with over 7 billion. Let's take a closer look!

**Which country is has the 0 population?**


```python
%%sql
SELECT *
    FROM facts
    WHERE population == (SELECT MIN(population)
                        FROM facts
                        );
```

    Done.





<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>250</td>
        <td>ay</td>
        <td>Antarctica</td>
        <td>None</td>
        <td>280000</td>
        <td>None</td>
        <td>0</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
</table>



Looks like it's Antartica!

**Which country has a population over 7 billion?**


```python
%%sql
SELECT *
    FROM facts
    WHERE population == (SELECT MAX(population)
                        FROM facts
                        );
```

    Done.





<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>261</td>
        <td>xx</td>
        <td>World</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>7256490011</td>
        <td>1.08</td>
        <td>18.6</td>
        <td>7.8</td>
        <td>None</td>
    </tr>
</table>



It looks like the table has a row for the whole world. Now that we know this, let's exclude it and recalulate our summary statistics from earlier!

**Step 3)** Recalculate our summary statistics and remove 'World'


```python
%%sql
SELECT MIN(population) AS min_pop,
        MAX(population) AS max_pop, 
        MIN(population_growth) AS min_pop_growth,
        MAX(population_growth) AS max_pop_growth
    FROM facts
    WHERE name != 'World';
        
```

    Done.





<table>
    <tr>
        <th>min_pop</th>
        <th>max_pop</th>
        <th>min_pop_growth</th>
        <th>max_pop_growth</th>
    </tr>
    <tr>
        <td>0</td>
        <td>1367485388</td>
        <td>0.0</td>
        <td>4.02</td>
    </tr>
</table>



**Step 4)** Calculate the average **population** and **area**


```python
%%sql
SELECT AVG(population) AS avg_pop,
        AVG(area) AS avg_area 
    FROM facts
    WHERE name != 'World';
```

    Done.





<table>
    <tr>
        <th>avg_pop</th>
        <th>avg_area</th>
    </tr>
    <tr>
        <td>32242666.56846473</td>
        <td>555093.546184739</td>
    </tr>
</table>



Looks like the average population is around 32 million and average area is 55000 $km^{2}$

**Step 5)** Find countries that are densley populated.    
We will need to calculate the countries with an above average population and below-average area.


```python
%%sql
SELECT *
    FROM facts
    WHERE population > (SELECT AVG(population)
                                  FROM facts
                                  WHERE name != 'World'
                       )                
    AND area < (SELECT AVG(area)
                       FROM facts
                       WHERE name != 'World'
               );
```

    Done.





<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>14</td>
        <td>bg</td>
        <td>Bangladesh</td>
        <td>148460</td>
        <td>130170</td>
        <td>18290</td>
        <td>168957745</td>
        <td>1.6</td>
        <td>21.14</td>
        <td>5.61</td>
        <td>0.46</td>
    </tr>
    <tr>
        <td>65</td>
        <td>gm</td>
        <td>Germany</td>
        <td>357022</td>
        <td>348672</td>
        <td>8350</td>
        <td>80854408</td>
        <td>0.17</td>
        <td>8.47</td>
        <td>11.42</td>
        <td>1.24</td>
    </tr>
    <tr>
        <td>80</td>
        <td>iz</td>
        <td>Iraq</td>
        <td>438317</td>
        <td>437367</td>
        <td>950</td>
        <td>37056169</td>
        <td>2.93</td>
        <td>31.45</td>
        <td>3.77</td>
        <td>1.62</td>
    </tr>
    <tr>
        <td>83</td>
        <td>it</td>
        <td>Italy</td>
        <td>301340</td>
        <td>294140</td>
        <td>7200</td>
        <td>61855120</td>
        <td>0.27</td>
        <td>8.74</td>
        <td>10.19</td>
        <td>4.1</td>
    </tr>
    <tr>
        <td>85</td>
        <td>ja</td>
        <td>Japan</td>
        <td>377915</td>
        <td>364485</td>
        <td>13430</td>
        <td>126919659</td>
        <td>0.16</td>
        <td>7.93</td>
        <td>9.51</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>91</td>
        <td>ks</td>
        <td>Korea, South</td>
        <td>99720</td>
        <td>96920</td>
        <td>2800</td>
        <td>49115196</td>
        <td>0.14</td>
        <td>8.19</td>
        <td>6.75</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>120</td>
        <td>mo</td>
        <td>Morocco</td>
        <td>446550</td>
        <td>446300</td>
        <td>250</td>
        <td>33322699</td>
        <td>1.0</td>
        <td>18.2</td>
        <td>4.81</td>
        <td>3.36</td>
    </tr>
    <tr>
        <td>138</td>
        <td>rp</td>
        <td>Philippines</td>
        <td>300000</td>
        <td>298170</td>
        <td>1830</td>
        <td>100998376</td>
        <td>1.61</td>
        <td>24.27</td>
        <td>6.11</td>
        <td>2.09</td>
    </tr>
    <tr>
        <td>139</td>
        <td>pl</td>
        <td>Poland</td>
        <td>312685</td>
        <td>304255</td>
        <td>8430</td>
        <td>38562189</td>
        <td>0.09</td>
        <td>9.74</td>
        <td>10.19</td>
        <td>0.46</td>
    </tr>
    <tr>
        <td>163</td>
        <td>sp</td>
        <td>Spain</td>
        <td>505370</td>
        <td>498980</td>
        <td>6390</td>
        <td>48146134</td>
        <td>0.89</td>
        <td>9.64</td>
        <td>9.04</td>
        <td>8.31</td>
    </tr>
    <tr>
        <td>173</td>
        <td>th</td>
        <td>Thailand</td>
        <td>513120</td>
        <td>510890</td>
        <td>2230</td>
        <td>67976405</td>
        <td>0.34</td>
        <td>11.19</td>
        <td>7.8</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>182</td>
        <td>ug</td>
        <td>Uganda</td>
        <td>241038</td>
        <td>197100</td>
        <td>43938</td>
        <td>37101745</td>
        <td>3.24</td>
        <td>43.79</td>
        <td>10.69</td>
        <td>0.74</td>
    </tr>
    <tr>
        <td>185</td>
        <td>uk</td>
        <td>United Kingdom</td>
        <td>243610</td>
        <td>241930</td>
        <td>1680</td>
        <td>64088222</td>
        <td>0.54</td>
        <td>12.17</td>
        <td>9.35</td>
        <td>2.54</td>
    </tr>
    <tr>
        <td>192</td>
        <td>vm</td>
        <td>Vietnam</td>
        <td>331210</td>
        <td>310070</td>
        <td>21140</td>
        <td>94348835</td>
        <td>0.97</td>
        <td>15.96</td>
        <td>5.93</td>
        <td>0.3</td>
    </tr>
</table>



We now have a table of some densely populated countries! Let's take this assignment a step further and make it easier to read.  

**1)** Keep the **name** column and add a **density** column for easy comparisons  
**2)** Sort by density so we can see which contries are those most dense in our selection  


```python
%%sql
SELECT name, 
    ROUND(CAST(population as Float) /CAST(area as Float), 2) AS density
    FROM facts
    WHERE population > (SELECT AVG(population)
                                  FROM facts
                                  WHERE name != 'World'
                       )                
    AND area < (SELECT AVG(area)
                       FROM facts
                       WHERE name != 'World'
               )
    ORDER BY density DESC;
```

    Done.





<table>
    <tr>
        <th>name</th>
        <th>density</th>
    </tr>
    <tr>
        <td>Bangladesh</td>
        <td>1138.07</td>
    </tr>
    <tr>
        <td>Korea, South</td>
        <td>492.53</td>
    </tr>
    <tr>
        <td>Philippines</td>
        <td>336.66</td>
    </tr>
    <tr>
        <td>Japan</td>
        <td>335.84</td>
    </tr>
    <tr>
        <td>Vietnam</td>
        <td>284.86</td>
    </tr>
    <tr>
        <td>United Kingdom</td>
        <td>263.08</td>
    </tr>
    <tr>
        <td>Germany</td>
        <td>226.47</td>
    </tr>
    <tr>
        <td>Italy</td>
        <td>205.27</td>
    </tr>
    <tr>
        <td>Uganda</td>
        <td>153.92</td>
    </tr>
    <tr>
        <td>Thailand</td>
        <td>132.48</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>123.33</td>
    </tr>
    <tr>
        <td>Spain</td>
        <td>95.27</td>
    </tr>
    <tr>
        <td>Iraq</td>
        <td>84.54</td>
    </tr>
    <tr>
        <td>Morocco</td>
        <td>74.62</td>
    </tr>
</table>



Now we can easily see from our selection of densley populated countries, which are the most dense. It appears Bangladesh is the most dense and Morocco is the least dense!
