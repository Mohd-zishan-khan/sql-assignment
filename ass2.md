```sql
-- 1

select oh.ORDER_ID,p.PARTY_ID,p.FIRST_NAME,p.LAST_NAME,pa.ADDRESS1,pa.CITY,pa.STATE_PROVINCE_GEO_ID,pa.POSTAL_CODE,pa.COUNTRY_GEO_ID,oh.STATUS_ID,oh.ORDER_DATE
from order_header oh 
left join order_role odr on odr.ORDER_ID = oh.ORDER_ID
left join person p on p.PARTY_ID = odr.PARTY_ID
left join order_contact_mech ocm on ocm.ORDER_ID =oh.ORDER_ID and ocm.CONTACT_MECH_PURPOSE_TYPE_ID ="SHIPPING_LOCATION"
left join postal_address pa on pa.CONTACT_MECH_ID =ocm.CONTACT_MECH_ID
where oh.STATUS_ID not in("ORDER_CANCELLED");



-- 2

select pa.CITY,oh.ORDER_ID,p.FIRST_NAME,p.LAST_NAME,oh.GRAND_TOTAL ,pa.ADDRESS1,pa.STATE_PROVINCE_GEO_ID,pa.POSTAL_CODE,pa.COUNTRY_GEO_ID,oh.STATUS_ID,oh.ORDER_DATE
from order_header oh 
left join order_role odr on odr.ORDER_ID = oh.ORDER_ID
left join person p on p.PARTY_ID = odr.PARTY_ID
left join order_contact_mech ocm on ocm.ORDER_ID =oh.ORDER_ID and ocm.CONTACT_MECH_PURPOSE_TYPE_ID ="SHIPPING_LOCATION"
left join postal_address pa on pa.CONTACT_MECH_ID =ocm.CONTACT_MECH_ID
where pa.CITY='NEW YORK'; 
	

-- 3

select PRODUCT_ID ,sum(QUANTITY ),CITY ,sum(GRAND_TOTAL)
from order_header oh 
left join order_item oi on oh.ORDER_ID=oi.ORDER_ID
left join order_contact_mech ocm on ocm.ORDER_ID =oh.ORDER_ID and ocm.CONTACT_MECH_PURPOSE_TYPE_ID ="SHIPPING_LOCATION"
left join postal_address pa on pa.CONTACT_MECH_ID =ocm.CONTACT_MECH_ID
where city="NEW YORK"
group by PRODUCT_ID ;

-- 4

select f.FACILITY_ID ,FACILITY_NAME ,count(oh.ORDER_ID ),sum(GRAND_TOTAL )
from order_header oh
left join order_item_ship_group oisg on oisg.ORDER_ID=oh.ORDER_ID
left join facility f on f.FACILITY_ID =oisg.FACILITY_ID
WHERE oh.ORDER_DATE BETWEEN '2024-01-01' AND '2024-1-31'
group by FACILITY_ID;

-- 5

select  ii.INVENTORY_ITEM_ID ,PRODUCT_ID ,FACILITY_ID ,iid.REASON_ENUM_ID 
from inventory_item ii 
left join inventory_item_detail iid on iid.INVENTORY_ITEM_ID = ii.INVENTORY_ITEM_ID
where iid.REASON_ENUM_ID in ('VAR_DAMAGED','VAR_LOST');

-- quantity lost or damaaged



-- 6

select ii.PRODUCT_ID , p.PRODUCT_NAME,ii.FACILITY_ID,ii.QUANTITY_ON_HAND,ii.AVAILABLE_TO_PROMISE,pf.MINIMUM_STOCK ,CURRENT_DATE AS DATE_CHECKED 
from inventory_item ii 
left join product p on p.PRODUCT_ID =ii.PARTY_ID
left join product_facility pf on pf.FACILITY_ID =ii.FACILITY_ID
WHERE 
    ii.QUANTITY_ON_HAND_TOTAL <= pf.REORDER_QUANTITY or ii.QUANTITY_ON_HAND_TOTAL <= 0
    OR ii.AVAILABLE_TO_PROMISE_TOTAL <= 0;




-- 7
select oh.ORDER_ID ,oh.STATUS_ID,f.FACILITY_ID ,f.FACILITY_NAME,f.FACILITY_TYPE_ID
from order_header oh 
left join order_item_ship_group oisg on oisg.ORDER_ID = oh.ORDER_ID
left join facility f on f.FACILITY_ID =oisg.FACILITY_ID
where oh.STATUS_ID in('ORDER_CREATED', 'ORDER_APPROVED', 'ORDER_HELD') and f.FACILITY_ID !='_NA_';


-- 8


select ii.PRODUCT_ID,ii.FACILITY_ID,ii.QUANTITY_ON_HAND,ii.AVAILABLE_TO_PROMISE,(ii.QUANTITY_ON_HAND-ii.AVAILABLE_TO_PROMISE)as difference
from inventory_item ii 
where (ii.QUANTITY_ON_HAND-ii.AVAILABLE_TO_PROMISE)!=0;




-- 9

SELECT os.order_id, os.order_item_seq_id, os.status_id AS current_status_id, os.status_datetime AS status_change_datetime, os.status_user_login AS change_by
FROM ORDER_STATUS AS os 
WHERE os.order_item_seq_id IS NOT NULL;

-- 10


SELECT oh.sales_channel_enum_id AS sales_channel, count(oh.order_id) AS total_orders, sum(oh.grand_total) AS total_revenue, 
        date_format(oh.order_date, '%m-%y') AS reporting_period
FROM ORDER_HEADER AS oh
WHERE oh.sales_channel_enum_id IS NOT NULL
GROUP BY sales_channel, reporting_period;

```






