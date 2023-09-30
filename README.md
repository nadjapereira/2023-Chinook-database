## 10 questions about the Chinook database 
This database is peculiar because contains various types of data such as music purchases, playlist trackers, clients and employees. Is good to practice some relationships using media as background and we don't see this very often.

#### The database Schema: 

![image](https://github.com/nadjapereira/2023-Chinook-database/assets/11997614/53dccb19-e14b-418d-8d39-662286b35742)

### 10 questions about the database:
#### 1. Create a consult showing the client's names, IDs, and countries outside the USA.
```sql
SELECT CustomerID, FirstName, LastName,  Country   
FROM customers  
WHERE Country <> 'EUA';
```

#### 2. Perform a consult showing the invoices from clients in Brazil with their name, invoice ID, and invoice date.
```sql
SELECT C.FirstName AS NomeCliente LastName AS SobrenomeCliente
I.InvoiceId
I.InvoiceDate
I.BillingCountry
FROM customers AS C
INNER JOIN
Invoices AS I ON C.CustomerID = I.CustomerID
WHERE C.Country = 'Brazil';
```

#### 3. How many invoices existed between 2009 and 2011?
```sql
SELECT
SUBSTR(InvoiceDate, 1, 4) AS Ano,
COUNT(*) AS TotalFaturas
FROM  Invoices
WHERE SUBSTR(InvoiceDate, 1, 4) IN ('2009', '2011')
GROUP BY Ano
ORDER BY Ano;
```

#### 4. Looking for invoice items, show the number of lines each one had
```sql
SELECT InvoiceID, COUNT(*) AS NumeroItens 
FROM Invoice_Items
GROUP BY InvoiceID 
ORDER BY InvoiceID; 
```

#### 5. Perform a consult which has the song purchase and their invoice
```sql
SELECT
I.InvoiceID,
IT.Quantity,
T.Name AS NomeDaFaixa
FROM Invoice_Items AS IT
INNER JOIN Tracks AS T ON IT.TrackID = T.TrackID
INNER JOIN Invoices AS I ON IT.InvoiceID = I.InvoiceID
```

#### 6. Perform a consult with the number of invoices by country
```sql
SELECT
C.Country AS Country,
COUNT(I.InvoiceID) AS NumeroDeFaturas
FROM Customers AS C
LEFT JOIN Invoices AS I ON C.CustomerID = I.CustomerID
GROUP BY C.Country
ORDER BY NumeroDeFaturas DESC;
```

#### 7. Which employee sold the most in 2009?
```sql
SELECT
E.FirstName || ' ' || E.LastName AS NomeDoAgente,
COUNT(I.InvoiceID) AS NumeroDeVendas
FROM Employees AS E
INNER JOIN Customers AS C ON E.EmployeeID = C.SupportRepID
INNER JOIN Invoices AS I ON C.CustomerID = I.CustomerID
WHERE strftime('%Y', I.InvoiceDate) = '2009'
GROUP BY NomeDoAgente
ORDER BY NumeroDeVendas DESC
LIMIT 1;
```

#### 8. Which employee sold the most in general?
```sql
SELECT
E.FirstName || ' ' || E.LastName AS NomeDoAgente,
COUNT(I.InvoiceID) AS NumeroDeVendas
FROM Employees AS E
INNER JOIN Customers AS C ON E.EmployeeID = C.SupportRepID
INNER JOIN Invoices AS I ON C.CustomerID = I.CustomerID
WHERE strftime('%Y', I.InvoiceDate) = '2009'
GROUP BY NomeDoAgente
ORDER BY NumeroDeVendas DESC
LIMIT 1;
```

##### 9. What country spends more money?
```sql
SELECT
C.Country AS Pais,
SUM(I.Total) AS TotalGasto
FROM Customers AS C
INNER JOIN Invoices AS I ON C.CustomerID = I.CustomerID
GROUP BY Pais
ORDER BY TotalGasto DESC
LIMIT 1
```

#### 10. Show the top 3 artists that sold the most
```sql
SELECT
A.Name AS NomeDoArtista,
SUM(I.Total) AS TotalVendido
FROM Artists AS A
INNER JOIN Albums AS Al ON A.ArtistID = Al.ArtistID
INNER JOIN Tracks AS T ON Al.AlbumID = T.AlbumID
INNER JOIN Invoice_Items AS II ON T.TrackID = II.TrackID
INNER JOIN Invoices AS I ON II.InvoiceID = I.InvoiceID
GROUP BY NomeDoArtista
ORDER BY TotalVendido DESC
LIMIT 3
```

