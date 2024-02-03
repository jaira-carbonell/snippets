# snippets for python

{
	// Place your snippets for python here. Each snippet is defined under a snippet name and has a prefix, body and 
	// description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
	// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the 
	// same ids are connected.
	// Example:
	// "Print to console": {
	// 	"prefix": "log",
	// 	"body": [
	// 		"console.log('$1');",
	// 		"$2"
	// 	],
	// 	"description": "Log output to console"
	// },

	"init py": {
		"prefix": "init.py",
		"body": [
"from flask import Flask",
"from flask_mysqldb import MySQL",
"",
"app = Flask(__name__)",
"",
"app.secret_key = 'jaira'",
"",
"app.config['MYSQL_HOST'] = 'localhost'",
"app.config['MYSQL_USER'] = 'root'",
"app.config['MYSQL_PASSWORD'] = ''",
"app.config['MYSQL_DB'] = 'onepiece_db'",
"",
"mysql = MySQL(app)",
"",
"from merch_app import routes",

		],
		"description": "execute init-py"
	},

	"to run": {
		"prefix": "run.py",
		"body": [
	"from merch_app import app",
		"if __name__ == '__main__':",
		"    app.run(debug=True)",

		],
		"description": "execute run.py"
	},

	"product-data": {
		"prefix": "index_route",
		"body": [
"from merch_app import app,mysql",
"from flask import jsonify, redirect, render_template, request, session",
"",
"",
"@app.route('/')",
"def index():",
"    cursor = mysql.connection.cursor()",
"    cursor.execute(\"SELECT * FROM products\")  # Replace 'products' with your actual table name",
"    product_data = cursor.fetchall()",
"    cursor.close()",
"    return render_template('index.html', products = product_data)",

		],
		"description": "route index"
	},

	"order-route": {
		"prefix": "order_route",
		"body": [
"			# Orders",
"app.route('/submit_order', methods=['POST'])",
"def submit_order():",
"   cursor = mysql.connection.cursor()",
"",   
"   try:",
"       # Extract data from the form",
"       product_id = int(request.form['productId'])",
"       quantity = float(request.form['quantity'])",
"       total_price = float(request.form['totalPrice'])",
"       mobile_number = request.form['mobileNumber']",
"",       
"       # Insert into orders table",
"       order_query = \"INSERT INTO orders (total_price, customer_contact) VALUES (%s, %s)\"",
"       cursor.execute(order_query, (total_price, mobile_number))",
"       order_id = cursor.connection.insert_id()  # Get the last inserted id",
"       ",
"       # Insert into order_items table",
"       order_items_query = \"INSERT INTO order_items (order_id, product_id, quantity, price) VALUES (%s, %s, %s, %s)\"",
"       cursor.execute(order_items_query, (order_id, product_id, quantity, total_price))",
"",
"       # Commit the transaction",
"       mysql.connection.commit()",
"",
"       #this line is to link to the alert of javascript",
"       session['order_message'] = \"Your order has been placed! Thank you.\"",
"       return redirect (\"/\")",
"   except Exception as e:",
"       # Rollback in case of error",
"       mysql.connection.rollback()",
"       return jsonify(success=False, error=str(e)), 500",
"   finally:",
"       # Close the cursor",
"       cursor.close()",
"       session['order_submitted'] = True",
"       return redirect (\"/\")",   
"",
		],
		"description": " order route" 
	},

	"display-orders": {
		"prefix": "d-orders_route",
		"body": [
"			    # Display orders",
"app.route('/orders')",
"def orders():",
"   cursor = mysql.connection.cursor()",
"   sql = \"\"",
"   SELECT ",
"       o.order_id, ",
"       o.date_time as order_date, ",
"       o.customer_contact, ",
"       o.total_price as order_total_price,",
"       p.name as product_name, ",
"       p.price_per_unit, ",
"       p.image_url, ",
"       oi.quantity",
"   FROM orders o",
"   JOIN order_items oi ON o.order_id = oi.order_id",
"   JOIN products p ON oi.product_id = p.product_id",
"   \"\"",
"   cursor.execute(sql)",
"   orders_data = cursor.fetchall()",
"   cursor.close()",
"",
"   formatted_orders_data = []",
"   for order in orders_data:",
"       # Format the order_date directly if it's already a datetime object",
"       formatted_order_date = order[1].strftime('%B %d, %Y at %I:%M:%S %p')",
"",
"       # Reconstruct the order tuple with the formatted date",
"       formatted_order = order[:1] + (formatted_order_date,) + order[2:]",
"       formatted_orders_data.append(formatted_order)",
"",
"   return render_template('orders.html', orders_data = formatted_orders_data)",

		],
		"description": "display-order route"
	}


}
