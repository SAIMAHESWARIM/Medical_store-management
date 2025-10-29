# Medical_store-management


# creation of table
import sqlite3 as sql
con=sql.connect('MedicineDB')
cur=con.cursor()
q='create table medical_shop(mid int primary key,mname text,category text,manufacture text,batch_num int,expire_date date,quant_available int, stor_loc text,cost real);'
cur.execute(q)
print('table created')
con.close()




# Insertion of data in Table
import sqlite3 as sql
con=sql.connect('MedicineDB')
cur=con.cursor()
q=' insert into medical_shop values(?,?,?,?,?,?,?,?,?);'
mid=int(input('enter medicine id:'))
mname=input('enter medicine name:')
category=input('enter medicine category:')
manufacture=input('enter medicine manufacturer:')
batch_num=int(input('enter medicine bath number:'))
expire_date=input('enter medicine expire date:')
quant_available=int(input('enter medicine quantity available in medical shop:'))
stor_loc=input('enter medicine store location:')
cost=float(input('enter medicine cost'))
t1=(mid,mname,category,manufacture,batch_num,expire_date,quant_available,stor_loc,cost)
cur.execute(q,t1)
data=cur.fetchone()
con.commit()
print('Values are inserted')
con.close()



# Executing operations on table data
import sqlite3 as sql
con=sql.connect('MedicineDB')
cur=con.cursor()
ch='y'
total=0
today=None
discount=12
purchased_items = []

while ch=='y':
    q='select * from medical_shop where mname=? and category=?  ;'
    mname=input('enter medical name:')
    category=input('enter medicine category:')
    subtotal=0


    t1=(mname,category)

    cur.execute(q,t1)
    data=cur.fetchall()
    new_stock=None


    if not data :
        print(f'Sorry!....{mname} {category} is not available in the database.')
    else:

        for row in data:
            expire_date=row[5]
            cost=row[8]

            gst=5.3
            quant_user = int(input('enter medicine quantity available in medical shop:'))
            # date_1=input('enter medicine date  upto which date you want:')
            quant_available=row[6]   # stock column
            if quant_user <= quant_available :

                print(f'{mname}, {category} - Available stock: {quant_available}')
                cur.execute("SELECT DATE('now');")
                today=cur.fetchone()[0]
                # if expire_date >= date_1:
                print('Before selling, check if expiry is within the next 30 days.')
                if expire_date >= today:

                    print(f'Expiry date upto{mname}: {expire_date}')
                    quantity1=quant_available-quant_user

                    subtotal=quant_user*cost
                    gst_amount=gst*subtotal/100
                    final_price=gst_amount+subtotal

                    total+=final_price
                    purchased_items.append((mname, quant_user, cost, subtotal))

                    cur.execute('update medical_shop set quant_available=? where mname=? and category=?',(quantity1,mname,category))

                    con.commit()

                    print(f'Updated quantity of {mname} ({category}) is: {quantity1}')
                    print('Updated successfully!')
                else:
                    print('--Sorry Medicine date will be expired--')
            else:
                print(f'Sorry!....{mname} stock is not Available (only {quant_available} left).')
                required_addition=quant_user-quant_available
                print(f'Adding {mname}: {required_addition}')
                ########### adding of stack ##############
                new_stock=required_addition+quant_available
                cur.execute('update medical_shop set quant_available=? where mname=? and category=?',(new_stock,mname,category))
                con.commit()
                print(f"Stock updated. New stock: {new_stock}")
                # print('bill after new_stock:',new_stock*cost)


    ch=input('Do you want to continue?(y/n):')
#### Bill Receipt ##########
print(' -- Here is the Bill Amount Receipt --')
print(f'Shop Name: Mahi Store && {today}')
print("\nPurchased Items:")
# Each item in purchased_item
for item in purchased_items:
    print(f"Name: {item[0]}, Quantity: {item[1]}, Price: {item[2]}, Final: {item[3]:.2f}")
##calculate total
if total > 0:
    if total>800:
        total_bill=total-((total*discount)/100)
        print(f'Total Bill: {total_bill}')
    else:
        print(f"Grand Total Bill Amount (with GST): {total:.2f}")
else:
    print("-- No valid purchases were made (all expired or unavailable)--.")

print('Thank you for visiting!.....')


con.close()

# OUTPUT

Enter medicine name: Paracetamol
Enter medicine category: Tablet
Enter quantity to purchase: 5
Available stock: 50
Expiry date: 2026-06-15
Updated stock: 45

--  Bill Receipt --
Shop Name: Mahi Store | Date: 2025-10-29

Purchased Items:
Name: Paracetamol, Quantity: 5, Price: 10.0, Subtotal: 50.00
Grand Total (with GST): ₹52.65
Thank you for visiting!..


