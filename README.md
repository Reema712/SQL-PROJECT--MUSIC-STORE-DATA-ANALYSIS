# SQL-PROJECT--MUSIC-STORE-DATA-ANALYSIS
                                                  

--1. Who is the senior most employee based on job title?

select * from Employee

Select Top 1 * from Employee
Order by title DESC

--2. Which countries have the most Invoices?

Select * from Invoice

Select count(billing_country) AS 'MOST INVOICES',  billing_country 
from Invoice
Group by billing_country 
ORDER BY billing_country  DESC


--3. What are top 3 values of total invoice?

Select * from Invoice

Select top 3 * from Invoice
Order by total DESC

/* Q4: Which city has the best customers? We would like to throw a promotional Music Festival in the city we made the most money. 
Write a query that returns one city that has the highest sum of invoice totals. 
Return both the city name & sum of all invoice totals */

SELECT * FROM Invoice

Select top 1 Sum(total) AS 'Total Iinvoices', Billing_city
from Invoice
GROUP BY Billing_city
ORDER BY 'Total Iinvoices' DESC 


/* Q5: Who is the best customer? The customer who has spent the most money will be declared the best customer. 
Write a query that returns the person who has spent the most money.*/

Select * from Customer 
select * from Invoice 

Select top 1 C.customer_id,C.first_name, C.last_name ,SUM(I.total) as ' total spend money' 
From Customer AS C
JOIN Invoice as I
ON C.customer_id = I.customer_id
group by C.customer_id ,C.first_name , C.last_name
order by ' total spend money' Desc 


/* Q6: Write query to return the email, first name, last name, & Genre of all Rock Music listeners. 
Return your list ordered alphabetically by email starting with A. */

Select DISTINCT C.email, C.first_name, C.last_name, G.name
from Customer AS C
JOIN Invoice as I
ON C.customer_id = I.customer_id
JOIN Invoice_line as IL on IL.invoice_id = I.invoice_id 
JOIN Track as T ON  T.track_id = IL.track_id
JOIN Genre as G ON G.genre_id = T.genre_id
WHERE G.name = 'Rock' 
order by C.email


/* Q7: Let's invite the artists who have written the most rock music in our dataset. 
Write a query that returns the Artist name and total track count of the top 10 rock bands. */

SELECT top 10 A.name,A.artist_id ,count(A.artist_id) as 'TOTAL COUNT OF BANDS'
FROM
Artist AS A
JOIN Album AS AL
ON A.artist_id= AL.artist_id
JOIN Track AS T ON T.album_id= AL.album_id
JOIN Genre as G ON T.genre_id = G.genre_id
WHERE G.name = 'Rock'
group by  A.name, A.artist_id 
ORDER BY 'TOTAL COUNT OF BANDS'  desc


/* Q8: Return all the track names that have a song length longer than the average song length. 
Return the Name and Milliseconds for each track. Order by the song length with the longest songs listed first. */

select * from Track 

select name, [milliseconds] from Track
where milliseconds >(
select avg(milliseconds)  AS avg_track_length from Track )
order by [milliseconds] Desc


/* Q9: Find how much amount spent by each customer on artists? Write a query to return customer name, artist name and total spent */

/* Steps to Solve: First, find which artist has earned the most according to the InvoiceLines. Now use this artist to find 
which customer spent the most on this artist. For this query, you will need to use the Invoice, InvoiceLine, Track, Customer, 
Album, and Artist tables. Note, this one is tricky because the Total spent in the Invoice table might not be on a single product, 
so you need to use the InvoiceLine table to find out how many of each product was purchased, and then multiply this by the price
for each artist. */


WITH Best_Selling_Artist as(
select TOP 1 A.artist_id, A.name, SUM(IL.[unit_price] * IL.[quantity]) AS 'TOTAL SALES'
from Artist as A 
JOIN Album AS AL 
ON A.artist_id = AL.artist_id
JOIN Track AS T ON T.album_id = AL.album_id
JOIN Invoice_line AS IL ON IL.track_id = T.track_id
GROUP BY  A.artist_id, A.name
ORDER BY 'TOTAL SALES' DESC
)

SELECT c.customer_id, c.first_name, c.last_name, bsa.name, SUM(il.unit_price*il.quantity) AS amount_spent
FROM Invoice i
JOIN customer c ON c.customer_id = i.customer_id
JOIN invoice_line il ON il.invoice_id = i.invoice_id
JOIN track t ON t.track_id = il.track_id
JOIN album alb ON alb.album_id = t.album_id
JOIN best_selling_artist bsa ON bsa.artist_id = alb.artist_id
GROUP BY c.customer_id, c.first_name, c.last_name, bsa.name
ORDER BY amount_spent DESC;


