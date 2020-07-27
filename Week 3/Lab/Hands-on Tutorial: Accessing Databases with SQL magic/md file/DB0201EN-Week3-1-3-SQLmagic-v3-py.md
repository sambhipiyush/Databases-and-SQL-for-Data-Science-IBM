<a href="https://www.bigdatauniversity.com"><img src = "https://ibm.box.com/shared/static/ugcqz6ohbvff804xp84y4kqnvvk3bq1g.png" width = 300, align = "center"></a>

<h1 align=center><font size = 5>Accessing Databases with SQL Magic</font></h1>

#### After using this notebook, you will know how to perform simplified database access using SQL "magic". You will connect to a Db2 database, issue SQL commands to create tables, insert data, and run queries, as well as retrieve results in a Python dataframe. 

##### To communicate with SQL Databases from within a JupyterLab notebook, we can use the SQL "magic" provided by the [ipython-sql](https://github.com/catherinedevlin/ipython-sql) extension. "Magic" is JupyterLab's term for special commands that start with "%". Below, we'll use the _load_\__ext_ magic to load the ipython-sql extension. In the lab environemnt provided in the course the ipython-sql extension is already installed and so is the ibm_db_sa driver. 


```python
%load_ext sql
```

##### Now we have access to SQL magic. With our first SQL magic command, we'll connect to a Db2 database. However, in order to do that, you'll first need to retrieve or create your credentials to access your Db2 database.

<a ><img src = "https://ibm.box.com/shared/static/uy78gy1uq3uj6fkvd4muzy5zcr62tb72.png" width = 1000, align = "center"></a>
  <h5 align=center>  This image shows the location of your connection string if you're using Db2 on IBM Cloud. If you're using another host the format is: username:password@hostname:port/database-name
  </h5>


```python
# Enter your Db2 credentials in the connection string below
# Recall you created Service Credentials in Part III of the first lab of the course in Week 1
# i.e. from the uri field in the Service Credentials copy everything after db2:// (but remove the double quote at the end)
# for example, if your credentials are as in the screenshot above, you would write:
# %sql ibm_db_sa://my-username:my-password@dashdb-txn-sbox-yp-dal09-03.services.dal.bluemix.net:50000/BLUDB
# Note the ibm_db_sa:// prefix instead of db2://
# This is because JupyterLab's ipython-sql extension uses sqlalchemy (a python SQL toolkit)
# which in turn uses IBM's sqlalchemy dialect: ibm_db_sa
%sql ibm_db_sa://USER-ID:PASSWORD@HOSTNAME:PORT-NUMBER/DATABASE-NAME
```




    'Connected: nrx71347@BLUDB'



##### For convenience, we can use %%sql (two %'s instead of one) at the top of a cell to indicate we want the entire cell to be treated as SQL. Let's use this to create a table and fill it with some test data for experimenting.


```python
%%sql

CREATE TABLE INTERNATIONAL_STUDENT_TEST_SCORES (
	country VARCHAR(50),
	first_name VARCHAR(50),
	last_name VARCHAR(50),
	test_score INT
);
INSERT INTO INTERNATIONAL_STUDENT_TEST_SCORES (country, first_name, last_name, test_score)
VALUES
('United States', 'Marshall', 'Bernadot', 54),
('Ghana', 'Celinda', 'Malkin', 51),
('Ukraine', 'Guillermo', 'Furze', 53),
('Greece', 'Aharon', 'Tunnow', 48),
('Russia', 'Bail', 'Goodwin', 46),
('Poland', 'Cole', 'Winteringham', 49),
('Sweden', 'Emlyn', 'Erricker', 55),
('Russia', 'Cathee', 'Sivewright', 49),
('China', 'Barny', 'Ingerson', 57),
('Uganda', 'Sharla', 'Papaccio', 55),
('China', 'Stella', 'Youens', 51),
('Poland', 'Julio', 'Buesden', 48),
('United States', 'Tiffie', 'Cosely', 58),
('Poland', 'Auroora', 'Stiffell', 45),
('China', 'Clarita', 'Huet', 52),
('Poland', 'Shannon', 'Goulden', 45),
('Philippines', 'Emylee', 'Privost', 50),
('France', 'Madelina', 'Burk', 49),
('China', 'Saunderson', 'Root', 58),
('Indonesia', 'Bo', 'Waring', 55),
('China', 'Hollis', 'Domotor', 45),
('Russia', 'Robbie', 'Collip', 46),
('Philippines', 'Davon', 'Donisi', 46),
('China', 'Cristabel', 'Radeliffe', 48),
('China', 'Wallis', 'Bartleet', 58),
('Moldova', 'Arleen', 'Stailey', 38),
('Ireland', 'Mendel', 'Grumble', 58),
('China', 'Sallyann', 'Exley', 51),
('Mexico', 'Kain', 'Swaite', 46),
('Indonesia', 'Alonso', 'Bulteel', 45),
('Armenia', 'Anatol', 'Tankus', 51),
('Indonesia', 'Coralyn', 'Dawkins', 48),
('China', 'Deanne', 'Edwinson', 45),
('China', 'Georgiana', 'Epple', 51),
('Portugal', 'Bartlet', 'Breese', 56),
('Azerbaijan', 'Idalina', 'Lukash', 50),
('France', 'Livvie', 'Flory', 54),
('Malaysia', 'Nonie', 'Borit', 48),
('Indonesia', 'Clio', 'Mugg', 47),
('Brazil', 'Westley', 'Measor', 48),
('Philippines', 'Katrinka', 'Sibbert', 51),
('Poland', 'Valentia', 'Mounch', 50),
('Norway', 'Sheilah', 'Hedditch', 53),
('Papua New Guinea', 'Itch', 'Jubb', 50),
('Latvia', 'Stesha', 'Garnson', 53),
('Canada', 'Cristionna', 'Wadmore', 46),
('China', 'Lianna', 'Gatward', 43),
('Guatemala', 'Tanney', 'Vials', 48),
('France', 'Alma', 'Zavittieri', 44),
('China', 'Alvira', 'Tamas', 50),
('United States', 'Shanon', 'Peres', 45),
('Sweden', 'Maisey', 'Lynas', 53),
('Indonesia', 'Kip', 'Hothersall', 46),
('China', 'Cash', 'Landis', 48),
('Panama', 'Kennith', 'Digance', 45),
('China', 'Ulberto', 'Riggeard', 48),
('Switzerland', 'Judy', 'Gilligan', 49),
('Philippines', 'Tod', 'Trevaskus', 52),
('Brazil', 'Herold', 'Heggs', 44),
('Latvia', 'Verney', 'Note', 50),
('Poland', 'Temp', 'Ribey', 50),
('China', 'Conroy', 'Egdal', 48),
('Japan', 'Gabie', 'Alessandone', 47),
('Ukraine', 'Devlen', 'Chaperlin', 54),
('France', 'Babbette', 'Turner', 51),
('Czech Republic', 'Virgil', 'Scotney', 52),
('Tajikistan', 'Zorina', 'Bedow', 49),
('China', 'Aidan', 'Rudeyeard', 50),
('Ireland', 'Saunder', 'MacLice', 48),
('France', 'Waly', 'Brunstan', 53),
('China', 'Gisele', 'Enns', 52),
('Peru', 'Mina', 'Winchester', 48),
('Japan', 'Torie', 'MacShirrie', 50),
('Russia', 'Benjamen', 'Kenford', 51),
('China', 'Etan', 'Burn', 53),
('Russia', 'Merralee', 'Chaperlin', 38),
('Indonesia', 'Lanny', 'Malam', 49),
('Canada', 'Wilhelm', 'Deeprose', 54),
('Czech Republic', 'Lari', 'Hillhouse', 48),
('China', 'Ossie', 'Woodley', 52),
('Macedonia', 'April', 'Tyer', 50),
('Vietnam', 'Madelon', 'Dansey', 53),
('Ukraine', 'Korella', 'McNamee', 52),
('Jamaica', 'Linnea', 'Cannam', 43),
('China', 'Mart', 'Coling', 52),
('Indonesia', 'Marna', 'Causbey', 47),
('China', 'Berni', 'Daintier', 55),
('Poland', 'Cynthia', 'Hassell', 49),
('Canada', 'Carma', 'Schule', 49),
('Indonesia', 'Malia', 'Blight', 48),
('China', 'Paulo', 'Seivertsen', 47),
('Niger', 'Kaylee', 'Hearley', 54),
('Japan', 'Maure', 'Jandak', 46),
('Argentina', 'Foss', 'Feavers', 45),
('Venezuela', 'Ron', 'Leggitt', 60),
('Russia', 'Flint', 'Gokes', 40),
('China', 'Linet', 'Conelly', 52),
('Philippines', 'Nikolas', 'Birtwell', 57),
('Australia', 'Eduard', 'Leipelt', 53)

```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.
    99 rows affected.





    []



#### Using Python Variables in your SQL Statements
##### You can use python variables in your SQL statements by adding a ":" prefix to your python variable names.
##### For example, if I have a python variable `country` with a value of `"Canada"`, I can use this variable in a SQL query to find all the rows of students from Canada.


```python
country = "Canada"
%sql select * from INTERNATIONAL_STUDENT_TEST_SCORES where country = :country
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>country</th>
        <th>first_name</th>
        <th>last_name</th>
        <th>test_score</th>
    </tr>
    <tr>
        <td>Canada</td>
        <td>Cristionna</td>
        <td>Wadmore</td>
        <td>46</td>
    </tr>
    <tr>
        <td>Canada</td>
        <td>Wilhelm</td>
        <td>Deeprose</td>
        <td>54</td>
    </tr>
    <tr>
        <td>Canada</td>
        <td>Carma</td>
        <td>Schule</td>
        <td>49</td>
    </tr>
</table>



#### Assigning the Results of Queries to Python Variables

##### You can use the normal python assignment syntax to assign the results of your queries to python variables.
##### For example, I have a SQL query to retrieve the distribution of test scores (i.e. how many students got each score). I can assign the result of this query to the variable `test_score_distribution` using the `=` operator.


```python
test_score_distribution = %sql SELECT test_score as "Test Score", count(*) as "Frequency" from INTERNATIONAL_STUDENT_TEST_SCORES GROUP BY test_score;
test_score_distribution
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>Test Score</th>
        <th>Frequency</th>
    </tr>
    <tr>
        <td>38</td>
        <td>2</td>
    </tr>
    <tr>
        <td>40</td>
        <td>1</td>
    </tr>
    <tr>
        <td>43</td>
        <td>2</td>
    </tr>
    <tr>
        <td>44</td>
        <td>2</td>
    </tr>
    <tr>
        <td>45</td>
        <td>8</td>
    </tr>
    <tr>
        <td>46</td>
        <td>7</td>
    </tr>
    <tr>
        <td>47</td>
        <td>4</td>
    </tr>
    <tr>
        <td>48</td>
        <td>14</td>
    </tr>
    <tr>
        <td>49</td>
        <td>8</td>
    </tr>
    <tr>
        <td>50</td>
        <td>10</td>
    </tr>
    <tr>
        <td>51</td>
        <td>8</td>
    </tr>
    <tr>
        <td>52</td>
        <td>8</td>
    </tr>
    <tr>
        <td>53</td>
        <td>8</td>
    </tr>
    <tr>
        <td>54</td>
        <td>5</td>
    </tr>
    <tr>
        <td>55</td>
        <td>4</td>
    </tr>
    <tr>
        <td>56</td>
        <td>1</td>
    </tr>
    <tr>
        <td>57</td>
        <td>2</td>
    </tr>
    <tr>
        <td>58</td>
        <td>4</td>
    </tr>
    <tr>
        <td>60</td>
        <td>1</td>
    </tr>
</table>



#### Converting Query Results to DataFrames

##### You can easily convert a SQL query result to a pandas dataframe using the `DataFrame()` method. Dataframe objects are much more versatile than SQL query result objects. For example, we can easily graph our test score distribution after converting to a dataframe.


```python
dataframe = test_score_distribution.DataFrame()

%matplotlib inline
# uncomment the following line if you get an module error saying seaborn not found
# !pip install seaborn
import seaborn

plot = seaborn.barplot(x='Test Score',y='Frequency', data=dataframe)
```


![png](output_16_0.png)


Now you know how to work with Db2 from within JupyterLab notebooks using SQL "magic"!


```python
%%sql 

-- Feel free to experiment with the data set provided in this notebook for practice:
SELECT country, first_name, last_name, test_score FROM INTERNATIONAL_STUDENT_TEST_SCORES;    
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>country</th>
        <th>first_name</th>
        <th>last_name</th>
        <th>test_score</th>
    </tr>
    <tr>
        <td>United States</td>
        <td>Marshall</td>
        <td>Bernadot</td>
        <td>54</td>
    </tr>
    <tr>
        <td>Ghana</td>
        <td>Celinda</td>
        <td>Malkin</td>
        <td>51</td>
    </tr>
    <tr>
        <td>Ukraine</td>
        <td>Guillermo</td>
        <td>Furze</td>
        <td>53</td>
    </tr>
    <tr>
        <td>Greece</td>
        <td>Aharon</td>
        <td>Tunnow</td>
        <td>48</td>
    </tr>
    <tr>
        <td>Russia</td>
        <td>Bail</td>
        <td>Goodwin</td>
        <td>46</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>Cole</td>
        <td>Winteringham</td>
        <td>49</td>
    </tr>
    <tr>
        <td>Sweden</td>
        <td>Emlyn</td>
        <td>Erricker</td>
        <td>55</td>
    </tr>
    <tr>
        <td>Russia</td>
        <td>Cathee</td>
        <td>Sivewright</td>
        <td>49</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Barny</td>
        <td>Ingerson</td>
        <td>57</td>
    </tr>
    <tr>
        <td>Uganda</td>
        <td>Sharla</td>
        <td>Papaccio</td>
        <td>55</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Stella</td>
        <td>Youens</td>
        <td>51</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>Julio</td>
        <td>Buesden</td>
        <td>48</td>
    </tr>
    <tr>
        <td>United States</td>
        <td>Tiffie</td>
        <td>Cosely</td>
        <td>58</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>Auroora</td>
        <td>Stiffell</td>
        <td>45</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Clarita</td>
        <td>Huet</td>
        <td>52</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>Shannon</td>
        <td>Goulden</td>
        <td>45</td>
    </tr>
    <tr>
        <td>Philippines</td>
        <td>Emylee</td>
        <td>Privost</td>
        <td>50</td>
    </tr>
    <tr>
        <td>France</td>
        <td>Madelina</td>
        <td>Burk</td>
        <td>49</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Saunderson</td>
        <td>Root</td>
        <td>58</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>Bo</td>
        <td>Waring</td>
        <td>55</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Hollis</td>
        <td>Domotor</td>
        <td>45</td>
    </tr>
    <tr>
        <td>Russia</td>
        <td>Robbie</td>
        <td>Collip</td>
        <td>46</td>
    </tr>
    <tr>
        <td>Philippines</td>
        <td>Davon</td>
        <td>Donisi</td>
        <td>46</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Cristabel</td>
        <td>Radeliffe</td>
        <td>48</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Wallis</td>
        <td>Bartleet</td>
        <td>58</td>
    </tr>
    <tr>
        <td>Moldova</td>
        <td>Arleen</td>
        <td>Stailey</td>
        <td>38</td>
    </tr>
    <tr>
        <td>Ireland</td>
        <td>Mendel</td>
        <td>Grumble</td>
        <td>58</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Sallyann</td>
        <td>Exley</td>
        <td>51</td>
    </tr>
    <tr>
        <td>Mexico</td>
        <td>Kain</td>
        <td>Swaite</td>
        <td>46</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>Alonso</td>
        <td>Bulteel</td>
        <td>45</td>
    </tr>
    <tr>
        <td>Armenia</td>
        <td>Anatol</td>
        <td>Tankus</td>
        <td>51</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>Coralyn</td>
        <td>Dawkins</td>
        <td>48</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Deanne</td>
        <td>Edwinson</td>
        <td>45</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Georgiana</td>
        <td>Epple</td>
        <td>51</td>
    </tr>
    <tr>
        <td>Portugal</td>
        <td>Bartlet</td>
        <td>Breese</td>
        <td>56</td>
    </tr>
    <tr>
        <td>Azerbaijan</td>
        <td>Idalina</td>
        <td>Lukash</td>
        <td>50</td>
    </tr>
    <tr>
        <td>France</td>
        <td>Livvie</td>
        <td>Flory</td>
        <td>54</td>
    </tr>
    <tr>
        <td>Malaysia</td>
        <td>Nonie</td>
        <td>Borit</td>
        <td>48</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>Clio</td>
        <td>Mugg</td>
        <td>47</td>
    </tr>
    <tr>
        <td>Brazil</td>
        <td>Westley</td>
        <td>Measor</td>
        <td>48</td>
    </tr>
    <tr>
        <td>Philippines</td>
        <td>Katrinka</td>
        <td>Sibbert</td>
        <td>51</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>Valentia</td>
        <td>Mounch</td>
        <td>50</td>
    </tr>
    <tr>
        <td>Norway</td>
        <td>Sheilah</td>
        <td>Hedditch</td>
        <td>53</td>
    </tr>
    <tr>
        <td>Papua New Guinea</td>
        <td>Itch</td>
        <td>Jubb</td>
        <td>50</td>
    </tr>
    <tr>
        <td>Latvia</td>
        <td>Stesha</td>
        <td>Garnson</td>
        <td>53</td>
    </tr>
    <tr>
        <td>Canada</td>
        <td>Cristionna</td>
        <td>Wadmore</td>
        <td>46</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Lianna</td>
        <td>Gatward</td>
        <td>43</td>
    </tr>
    <tr>
        <td>Guatemala</td>
        <td>Tanney</td>
        <td>Vials</td>
        <td>48</td>
    </tr>
    <tr>
        <td>France</td>
        <td>Alma</td>
        <td>Zavittieri</td>
        <td>44</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Alvira</td>
        <td>Tamas</td>
        <td>50</td>
    </tr>
    <tr>
        <td>United States</td>
        <td>Shanon</td>
        <td>Peres</td>
        <td>45</td>
    </tr>
    <tr>
        <td>Sweden</td>
        <td>Maisey</td>
        <td>Lynas</td>
        <td>53</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>Kip</td>
        <td>Hothersall</td>
        <td>46</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Cash</td>
        <td>Landis</td>
        <td>48</td>
    </tr>
    <tr>
        <td>Panama</td>
        <td>Kennith</td>
        <td>Digance</td>
        <td>45</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Ulberto</td>
        <td>Riggeard</td>
        <td>48</td>
    </tr>
    <tr>
        <td>Switzerland</td>
        <td>Judy</td>
        <td>Gilligan</td>
        <td>49</td>
    </tr>
    <tr>
        <td>Philippines</td>
        <td>Tod</td>
        <td>Trevaskus</td>
        <td>52</td>
    </tr>
    <tr>
        <td>Brazil</td>
        <td>Herold</td>
        <td>Heggs</td>
        <td>44</td>
    </tr>
    <tr>
        <td>Latvia</td>
        <td>Verney</td>
        <td>Note</td>
        <td>50</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>Temp</td>
        <td>Ribey</td>
        <td>50</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Conroy</td>
        <td>Egdal</td>
        <td>48</td>
    </tr>
    <tr>
        <td>Japan</td>
        <td>Gabie</td>
        <td>Alessandone</td>
        <td>47</td>
    </tr>
    <tr>
        <td>Ukraine</td>
        <td>Devlen</td>
        <td>Chaperlin</td>
        <td>54</td>
    </tr>
    <tr>
        <td>France</td>
        <td>Babbette</td>
        <td>Turner</td>
        <td>51</td>
    </tr>
    <tr>
        <td>Czech Republic</td>
        <td>Virgil</td>
        <td>Scotney</td>
        <td>52</td>
    </tr>
    <tr>
        <td>Tajikistan</td>
        <td>Zorina</td>
        <td>Bedow</td>
        <td>49</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Aidan</td>
        <td>Rudeyeard</td>
        <td>50</td>
    </tr>
    <tr>
        <td>Ireland</td>
        <td>Saunder</td>
        <td>MacLice</td>
        <td>48</td>
    </tr>
    <tr>
        <td>France</td>
        <td>Waly</td>
        <td>Brunstan</td>
        <td>53</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Gisele</td>
        <td>Enns</td>
        <td>52</td>
    </tr>
    <tr>
        <td>Peru</td>
        <td>Mina</td>
        <td>Winchester</td>
        <td>48</td>
    </tr>
    <tr>
        <td>Japan</td>
        <td>Torie</td>
        <td>MacShirrie</td>
        <td>50</td>
    </tr>
    <tr>
        <td>Russia</td>
        <td>Benjamen</td>
        <td>Kenford</td>
        <td>51</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Etan</td>
        <td>Burn</td>
        <td>53</td>
    </tr>
    <tr>
        <td>Russia</td>
        <td>Merralee</td>
        <td>Chaperlin</td>
        <td>38</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>Lanny</td>
        <td>Malam</td>
        <td>49</td>
    </tr>
    <tr>
        <td>Canada</td>
        <td>Wilhelm</td>
        <td>Deeprose</td>
        <td>54</td>
    </tr>
    <tr>
        <td>Czech Republic</td>
        <td>Lari</td>
        <td>Hillhouse</td>
        <td>48</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Ossie</td>
        <td>Woodley</td>
        <td>52</td>
    </tr>
    <tr>
        <td>Macedonia</td>
        <td>April</td>
        <td>Tyer</td>
        <td>50</td>
    </tr>
    <tr>
        <td>Vietnam</td>
        <td>Madelon</td>
        <td>Dansey</td>
        <td>53</td>
    </tr>
    <tr>
        <td>Ukraine</td>
        <td>Korella</td>
        <td>McNamee</td>
        <td>52</td>
    </tr>
    <tr>
        <td>Jamaica</td>
        <td>Linnea</td>
        <td>Cannam</td>
        <td>43</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Mart</td>
        <td>Coling</td>
        <td>52</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>Marna</td>
        <td>Causbey</td>
        <td>47</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Berni</td>
        <td>Daintier</td>
        <td>55</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>Cynthia</td>
        <td>Hassell</td>
        <td>49</td>
    </tr>
    <tr>
        <td>Canada</td>
        <td>Carma</td>
        <td>Schule</td>
        <td>49</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>Malia</td>
        <td>Blight</td>
        <td>48</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Paulo</td>
        <td>Seivertsen</td>
        <td>47</td>
    </tr>
    <tr>
        <td>Niger</td>
        <td>Kaylee</td>
        <td>Hearley</td>
        <td>54</td>
    </tr>
    <tr>
        <td>Japan</td>
        <td>Maure</td>
        <td>Jandak</td>
        <td>46</td>
    </tr>
    <tr>
        <td>Argentina</td>
        <td>Foss</td>
        <td>Feavers</td>
        <td>45</td>
    </tr>
    <tr>
        <td>Venezuela</td>
        <td>Ron</td>
        <td>Leggitt</td>
        <td>60</td>
    </tr>
    <tr>
        <td>Russia</td>
        <td>Flint</td>
        <td>Gokes</td>
        <td>40</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Linet</td>
        <td>Conelly</td>
        <td>52</td>
    </tr>
    <tr>
        <td>Philippines</td>
        <td>Nikolas</td>
        <td>Birtwell</td>
        <td>57</td>
    </tr>
    <tr>
        <td>Australia</td>
        <td>Eduard</td>
        <td>Leipelt</td>
        <td>53</td>
    </tr>
</table>



Copyright &copy; 2018 [cognitiveclass.ai](cognitiveclass.ai?utm_source=bducopyrightlink&utm_medium=dswb&utm_campaign=bdu). This notebook and its source code are released under the terms of the [MIT License](https://bigdatauniversity.com/mit-license/).

