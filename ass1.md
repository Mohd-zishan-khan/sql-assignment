```sql
-- 1
SELECT DISTINCT
    p.PARTY_ID,
    per.FIRST_NAME,
    per.LAST_NAME,
    email_cm .INFO_STRING AS email, 
    tn.CONTACT_NUMBER,
    p.CREATED_DATE
FROM Party AS p
JOIN Person AS per ON p.PARTY_ID = per.PARTY_ID
JOIN Party_Role AS pr ON pr.PARTY_ID = p.PARTY_ID AND pr.ROLE_TYPE_ID = 'CUSTOMER'
LEFT JOIN Party_Contact_Mech pcm_email ON pcm_email.PARTY_ID = p.PARTY_ID
LEFT JOIN Contact_Mech email_cm ON email_cm.CONTACT_MECH_ID = pcm_email.CONTACT_MECH_ID AND email_cm.CONTACT_MECH_TYPE_ID = 'EMAIL_ADDRESS'
LEFT JOIN Party_Contact_Mech pcm_ph ON pcm_ph.PARTY_ID = p.PARTY_ID
LEFT JOIN Contact_Mech phone_cm ON phone_cm.CONTACT_MECH_ID = pcm_ph.CONTACT_MECH_ID AND phone_cm.CONTACT_MECH_TYPE_ID = 'TELECOM_NUMBER'
LEFT JOIN Telecom_Number tn ON tn.CONTACT_MECH_ID = phone_cm.CONTACT_MECH_ID
WHERE
    p.CREATED_DATE >= '2023-06-01 00:00:00.000'and p.CREATED_DATE <'2023-07-01 00:00:00.000';
    



-- 2
select p.PRODUCT_ID ,p.PRODUCT_TYPE_ID ,p.INTERNAL_NAME  
from product p   
join product_type pt on p.PRODUCT_TYPE_ID =pt.PRODUCT_TYPE_ID
where pt.IS_PHYSICAL ="Y";


-- 3

select p.PRODUCT_ID,p.INTERNAL_NAME ,p.PRODUCT_TYPE_ID ,gi.ID_VALUE 
from product p
left join good_identification gi  on p.PRODUCT_ID =gi.PRODUCT_ID
where gi.GOOD_IDENTIFICATION_TYPE_ID ="ERP_ID" and gi.ID_VALUE is NULL;



-- 4

SELECT 
    p.PRODUCT_ID,
    shopifyId.ID_VALUE AS SHOPIFY_ID,
    p.PRODUCT_ID AS HOTWAX_ID,
    erpId.ID_VALUE AS ERP_ID
FROM 
    PRODUCT p
LEFT JOIN GOOD_IDENTIFICATION shopifyId 
    ON p.PRODUCT_ID = shopifyId.PRODUCT_ID 
    AND shopifyId.GOOD_IDENTIFICATION_TYPE_ID = 'SHOPIFY_PROD_ID'
LEFT JOIN GOOD_IDENTIFICATION erpId 
    ON p.PRODUCT_ID = erpId.PRODUCT_ID 
    AND erpId.GOOD_IDENTIFICATION_TYPE_ID = 'ERP_ID';        


-- 5
select distinct(os.STATUS_ID )
from order_status os ;

  select oi.PRODUCT_ID ,p.PRODUCT_TYPE_ID ,psc.PRODUCT_STORE_ID ,oi.QUANTITY,p.INTERNAL_NAME
         ,oi.EXTERNAL_ID,oi.ORDER_ID,oi.ORDER_ITEM_SEQ_ID,oi.SHIP_GROUP_SEQ_ID,f.FACILITY_ID,f.FACILITY_TYPE_ID,oh.ORDER_HISTORY_ID  
   from order_item oi 
   join order_status os on os.ORDER_ID =oi.ORDER_ID  and  os.ORDER_STATUS_ID ="ORDER_COMPLETED" and  os.STATUS_DATETIME >'2023-08-01 00:00:00.000' and os.STATUS_DATETIME <'2023-09-01 00:00:00.000'
   join product p on oi.PRODUCT_ID =p.PRODUCT_ID
   join product_store_catalog psc  on psc.PROD_CATALOG_ID =oi.PROD_CATALOG_ID
   join facility f  on f.PRODUCT_STORE_ID =psc.PRODUCT_STORE_ID
   join order_history oh on oh.ORDER_ID =oi.ORDER_ID;
  
--   7
  
  select oh.ORDER_ID,oh.GRAND_TOTAL,opp.PAYMENT_METHOD_TYPE_ID as payment_meth,oh.EXTERNAL_ID as NETSUIT_ID
  from order_header oh 
  left join order_payment_preference opp on oh.ORDER_ID =opp.ORDER_ID
  where oh.STATUS_ID ="ORDER_CREATED";
  
  
--  8
  

select oh.ORDER_ID,oh.STATUS_ID as order_Status,opp.STATUS_ID as pay_Status,ss.STATUS_ID as shipment_Status  
from order_header oh 
left join order_payment_preference opp on opp.ORDER_ID =oh.ORDER_ID
left join order_shipment os on os.ORDER_ID =oh.ORDER_ID
left join shipment_status ss on ss.SHIPMENT_ID =os.SHIPMENT_ID
where opp.status_id="PAYMENT_RECEIVED" and (ss.shipment_status_id not in("SHIPMENT_DELIVERED") or ss.SHIPMENT_STATUS_ID IS NULL);



-- 9

select HOUR(os.STATUS_DATETIME) as hours,count(ORDER_ID )
from order_status os 
where os.STATUS_ID ="ORDER_COMPLETED"
group by hours
order by hours ;


-- 10


SELECT  COUNT(*) AS total_orders,SUM(oi.UNIT_PRICE *oi.quantity) AS total_revenue
FROM order_item oi
join order_header oh on oh.ORDER_ID =oi.ORDER_ID 
LEFT JOIN order_item_ship_group oisg ON oisg.ORDER_ID = oi.ORDER_ID and oisg.SHIP_GROUP_SEQ_ID = oi.SHIP_GROUP_SEQ_ID 
WHERE oisg.SHIPMENT_METHOD_TYPE_ID = 'STOREPICKUP'
    AND YEAR(oh.ENTRY_DATE) = YEAR(CURDATE()) - 1;


select * from order_item ;

-- 11

select os.CHANGE_REASON,count(oh.ORDER_ID )
FROM order_header oh 
join order_status os on os.ORDER_ID = oh.ORDER_ID 
where os.STATUS_ID="ORDER_CANCELLED" AND MONTH(os.status_datetime) = month(CURRENT_DATE())-1
group by os.CHANGE_REASON 
order by  os.CHANGE_REASON; 
    

-- 12

select pf.PRODUCT_ID ,pf.MINIMUM_STOCK as THRESHOLD
from PRODUCT_FACILITY pf
where pf.MINIMUM_STOCK > 0;

```

