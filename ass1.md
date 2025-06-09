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
    p.CREATED_DATE >= '2023-06-01 00:00:00.000';
    


select

-- 2
select p.PRODUCT_ID ,p.PRODUCT_TYPE_ID ,p.INTERNAL_NAME  
from product p   
left join product_type pt on p.PRODUCT_TYPE_ID =pt.PRODUCT_TYPE_ID
where pt.IS_PHYSICAL ="Y";


-- 3


select p.PRODUCT_ID,p.INTERNAL_NAME ,p.PRODUCT_TYPE_ID ,gi.ID_VALUE 
from product p
left join good_identification gi  on p.PRODUCT_ID =gi.PRODUCT_ID
where gi.GOOD_IDENTIFICATION_TYPE_ID ="ERP_ID";




-- 4

select p.PRODUCT_ID ,oi.EXTERNAL_ID as SHOPIFY_ID,p.PRODUCT_ID as HOTWAX_ID ,gi.ID_VALUE as NETSUIT_ID
from order_item oi 
left join product p  on oi.PRODUCT_ID =p.PRODUCT_ID
left join good_identification gi  on gi.PRODUCT_ID =p.PRODUCT_ID
where gi.GOOD_IDENTIFICATION_TYPE_ID ="ERP_ID";


-- 5

  select oi.PRODUCT_ID ,p.PRODUCT_TYPE_ID ,psc.PRODUCT_STORE_ID ,oi.QUANTITY,p.INTERNAL_NAME
         ,oi.EXTERNAL_ID,oi.ORDER_ID,oi.ORDER_ITEM_SEQ_ID,oi.SHIP_GROUP_SEQ_ID,f.FACILITY_ID,f.FACILITY_TYPE_ID,oh.ORDER_HISTORY_ID  
   from order_item oi 
   left join product p on oi.PRODUCT_ID =p.PRODUCT_ID
   left join product_store_catalog psc  on psc.PROD_CATALOG_ID =oi.PROD_CATALOG_ID
   left join facility f  on f.PRODUCT_STORE_ID =psc.PRODUCT_STORE_ID
   left join order_history oh on oh.ORDER_ID =oi.ORDER_ID;
  
--   7
  
  select oh.ORDER_ID,oh.GRAND_TOTAL,opp.PAYMENT_METHOD_TYPE_ID as payment_meth,gi.ID_VALUE as NETSUIT_ID
  from order_header oh 
  left join order_item oi on oh.ORDER_ID =oi.ORDER_ID
  left join good_identification gi on gi.PRODUCT_ID =oi.PRODUCT_ID
  left join order_payment_preference opp on oh.ORDER_ID =opp.ORDER_ID 
  where gi.GOOD_IDENTIFICATION_TYPE_ID ="ERP_ID";
  
  
  
  
--  8
  

select oh.ORDER_ID,oh.STATUS_ID,opp.STATUS_ID,ss.STATUS_ID  
from order_header oh 
left join order_payment_preference opp on opp.ORDER_ID =oh.ORDER_ID
left join order_shipment os on os.ORDER_ID =oh.ORDER_ID
left join shipment_status ss on ss.SHIPMENT_ID =os.SHIPMENT_ID
where opp.status_id="PAYMENT_RECEIVED" and ss.shipment_status_id not in("SHIPMENT_DELIVERED");



-- 9

select HOUR(os.STATUS_DATETIME) as hours,count(ORDER_ID )
from order_status os 
where os.STATUS_ID ="ORDER_COMPLETED"
group by hour(os.STATUS_DATETIME)
order by hours ;


-- 10


SELECT  COUNT(*) AS total_orders,SUM(oh.GRAND_TOTAL) AS total_revenue
FROM order_header oh
LEFT JOIN order_item_ship_group oisg ON oisg.ORDER_ID = oh.ORDER_ID
WHERE  
    oh.SALES_CHANNEL_ENUM_ID = 'WEB_SALES_CHANNEL'
    AND oisg.SHIPMENT_METHOD_TYPE_ID = 'STOREPICKUP'
    AND YEAR(oh.ENTRY_DATE) = 2023;

-- 11

select os.CHANGE_REASON,count(oh.ORDER_ID )
FROM order_header oh 
left join order_status os on os.ORDER_ID = oh.ORDER_ID 
where oh.STATUS_ID="ORDER_CANCELLED" AND MONTH(os.status_datetime) = 04
    AND YEAR(os.status_datetime) = 2024
group by os.CHANGE_REASON 
order by  os.CHANGE_REASON; 
    


select *
FROM order_status os 
where STATUS_ID ="ORDER_CANCELLED" and ORDER_ITEM_SEQ_ID !="NULL";


-- 12

-- how to get threshold


