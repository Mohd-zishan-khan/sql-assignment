
--   1
select oh.ORDER_ID ,oi.ORDER_ITEM_SEQ_ID ,p.PRODUCT_ID ,p.PRODUCT_TYPE_ID ,oh.SALES_CHANNEL_ENUM_ID
       ,oh.ORDER_DATE,oh.ENTRY_DATE,os.STATUS_ID,os.STATUS_DATETIME ,oh.ORDER_TYPE_ID,oh.PRODUCT_STORE_ID 
from order_item oi 
left join order_header oh on oh.ORDER_ID=oi.ORDER_ID  
left join product p on oi.PRODUCT_ID =p.PRODUCT_ID
left join order_status os on os.ORDER_ID =oh.ORDER_ID
where oi.ORDER_ITEM_SEQ_ID !='NULL'and os.STATUS_ID not in ('ITEM_COMPLETED','ITEM_CANCELLED','ORDER_CANCELLED','ORDER_COMPLETED') 
      AND p.PRODUCT_TYPE_ID = 'FINISHED_GOOD';


-- 2

select ri.RETURN_ID,ri.ORDER_ID,oh.PRODUCT_STORE_ID ,rs.STATUS_DATETIME ,oh.ORDER_NAME,rh.FROM_PARTY_ID ,rh.RETURN_DATE ,rh.ENTRY_DATE ,rh.RETURN_CHANNEL_ENUM_ID
from return_item ri 
left join order_header oh on oh.ORDER_ID =ri.ORDER_ID
left join return_status rs on rs.RETURN_ID =ri.RETURN_ID
left join return_header rh  on rh.RETURN_ID =ri.RETURN_ID
where rs.STATUS_ID ='RETURN_COMPLETED';
-- for seeing channel is available or  not
select * from return_header rh ; 




-- 3

select rh.FROM_PARTY_ID,p.FIRST_NAME
from return_header rh 
left join person p on p.PARTY_ID =rh.FROM_PARTY_ID
where MONTH(rh.ENTRY_DATE ) = 03
    AND year(rh.ENTRY_DATE) = 2024
group by FROM_PARTY_ID 
having COUNT(*)=1;

-- 4

select count(ri.RETURN_ID ) total_return_order,sum(ri.RETURN_PRICE) total_return_amount,count(ra.RETURN_ADJUSTMENT_ID),sum(ra.AMOUNT)
from return_item ri 
left join return_adjustment ra on ra.RETURN_ID =ri.RETURN_ID;




-- 5

select ra.RETURN_ID,date(rh.ENTRY_DATE) ,ra.RETURN_ADJUSTMENT_TYPE_ID,ri.RETURN_PRICE ,ri.ORDER_ID ,date(oh.ORDER_DATE) 
       ,rh.RETURN_DATE,oh.PRODUCT_STORE_ID
from return_header rh 
left join return_adjustment ra on ra.RETURN_ID =rh.RETURN_ID
left join return_item ri  on ri.RETURN_ID =rh.RETURN_ID
left join order_header oh on oh.ORDER_ID =ri.ORDER_ID;


-- 6

select ri.ORDER_ID ,ri.RETURN_ID ,rh.RETURN_DATE,ri.RETURN_REASON_ID,ri.RETURN_QUANTITY
from return_item ri 
left join return_header rh on rh.RETURN_ID =ri.RETURN_ID
WHERE 
    ri.ORDER_ID IN (
        SELECT ORDER_ID
        FROM return_item
        GROUP BY ORDER_ID
        HAVING COUNT(*) > 1
    );

-- 7


select f.FACILITY_ID,f.FACILITY_NAME,count(*)as total_next_day_del
from facility f 
left join picklist p on p.FACILITY_ID =f.FACILITY_ID
where p.SHIPMENT_METHOD_TYPE_ID ="NEXT_DAY"
group by f.FACILITY_ID;


-- 8


select p.PARTY_ID,concat(per.first_name,' ', per.last_name) as name,fgr.ROLE_TYPE_ID,fgm.FACILITY_ID,p.STATUS_ID
from facility_group_role fgr 
left join person per on fgr.PARTY_ID  =per.PARTY_ID
left join facility_group_member fgm on fgr.FACILITY_GROUP_ID =fgm.FACILITY_GROUP_ID 
left join party p  on p.PARTY_ID =per.PARTY_ID



-- 9

select p.PRODUCT_ID ,p.PRODUCT_NAME,count(ii.FACILITY_ID )
from inventory_item ii 
left join product p on p.PRODUCT_ID =ii.PRODUCT_ID 
where ii.QUANTITY_ON_HAND_TOTAL >0
group by ii.PRODUCT_ID ;


-- 10

SELECT ii.PRODUCT_ID,ii.FACILITY_ID,f.FACILITY_TYPE_ID,ii.QUANTITY_ON_HAND_TOTAL AS QOH,ii.AVAILABLE_TO_PROMISE_TOTAL AS ATP,
    (ii.DATETIME_RECEIVED )
FROM inventory_item ii
left join facility f ON ii.FACILITY_ID = f.FACILITY_ID
WHERE  f.FACILITY_TYPE_ID != 'VIRTUAL' 
order by ii.PRODUCT_ID;



-- 11

SELECT 
    it.INVENTORY_TRANSFER_ID AS TRANSFER_ORDER_ID,
    it.FACILITY_ID AS FROM_FACILITY_ID,
    it.FACILITY_ID_TO AS TO_FACILITY_ID,
    it.PRODUCT_ID,
    it.QUANTITY AS REQUESTED_QUANTITY,
    oisgir.QUANTITY as reserved_quantity,
    it.SEND_DATE ,
    it.STATUS_ID 
FROM 
    inventory_transfer it
left join order_item_ship_grp_inv_res oisgir on oisgir.INVENTORY_ITEM_ID =it.INVENTORY_ITEM_ID    
WHERE 
    it.STATUS_ID NOT IN ('IX_CANCELLED', 'IX_COMPLETED')
    AND (oisgir.QUANTITY IS NULL OR oisgir.QUANTITY < it.QUANTITY)
ORDER BY 
    it.send_DATE DESC;



