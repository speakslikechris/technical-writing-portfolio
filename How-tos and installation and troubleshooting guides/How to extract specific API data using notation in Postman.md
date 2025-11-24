# How to extract specific API data using notation in Postman
I use Postman for API documentation. So, testing APIs and documenting them. I don't have a highly technical background, even though I'm familiar enough with languages such as XML and JSON to read and manipulate them.
However, I ran into an issue with Postman, slowing down my API testing.

## The problem
Postman is great for testing APIs across multiple methods (GET, PUT, DELETE, PATCH, etc). However, I couldn't directly fetch specific responses using the **dot notation** as I would with a command line interface, for example.
This isn't a data filtration problem. For that, you could just set parameters in Postman. Instead, I wanted to retrieve specific API data such that responses wouldn't output the entire JSON text, but a specific data type.

## Example of the problem
Let's say I'm working with an OpenAPI spec like the OpenWeatherAPI. I run the `GET` request below to return weather data for a specific ZIP code:
```
GET
api.openweathermap.org/data/2.5/weather?zip=95050&units=imperial&appid={{YOUR-API-TOKEN}}
```
A sample JSON response is presented below:
```
{
	"coord": {
		"lon": -121.953,
		"lat": 37.3492
	},
	"weather": [
		{
			"id": 803,
			"main": "Clouds",
			"description": "broken clouds",
			"icon": "04d"
		}
	],
	"base": "stations",
	"main": {
		"temp": 55.71,
		"feels_like": 55.02,
		"temp_min": 54.05,
		"temp_max": 58.51,
		"pressure": 1014,
		"humidity": 86,
		"sea_level": 1014,
		"grnd_level": 992
	},
	"visibility": 10000,
	"wind": {
		"speed": 10.36,
		"deg": 320
	},
	"clouds": {
		"all": 75
	},
	"dt": 1763676155,
	"sys": {
		"type": 1,
		"id": 5845,
		"country": "US",
		"sunrise": 1763650374,
		"sunset": 1763686470
	},
	"timezone": -28800,
	"id": 0,
	"name": "Santa Clara",
	"cod": 200
}
```
In this case, I'd like to document how developers can get a certain result, such as the windspeed or weather description, printed or displayed in their applications.
You can easily achieve this in cURL using dot notation. But what if you wanted to do the same thing within Postman?
That's what I struggled with, and none of the Postman forum answers on this did it for me. So, I did some testing on my own till I figured it out.

## The solution
The best approach I found is to configure a **Post-response** script in Postman. This script combs through the data returned via the original API request to display specified information.
Here's how:
1. Call the target API using a preferred method. This step ensures you have the right access to the API.
2. Click `Scripts` in the Postman menu below the URI field.
[![Scripts tab in Postman](https://i.postimg.cc/5yLz3XF3/SCR_20251120_otrd.png)](https://postimg.cc/vx8D8Hhg)

3. Select `Post-response`.

[![SCR_20251120_otrd.png](https://i.postimg.cc/1XwFH8NK/SCR_20251120_otrd.png)](https://postimg.cc/7JPbPY9C)

4. Set a constant to represent your JSON response. The following sample code shows how I approach this.
   
`const responseData=pm.response.json();`

[![JSON script to use](https://i.postimg.cc/kGKSc2tS/SCR_20251120_otrd.png)](https://postimg.cc/n9VMVc2V)

>[!IMPORTANT]
>'responseData' is a random constant. You can use any other constant you prefer.

5. Set up a constant to define the data returned in the output. The following sample code shows how I approach this. I'll create a `content` constant for this example.
   
`const content=responseData.weather[0].main`
>[!NOTE]
> Step 5 is an optional step. It helps me to keep tests organized, and you may not need it.
6. Define the console log's output. This is the data you want to extract and display from the API's general response. Here's how I defined mine to return the weather type:

`console.log("Main Weather: " + content);` and `console.log("windspeed: " + content);`
>[!NOTE]
> If you didn't define a constant such as `content` from Step 5 above, you can't declare it in Step 6. Instead, declare the entire path to the item you want returned in your application.
7. Now, run the API call again by clicking `Send` in Postman.

[![Send button highlighted to make API call in Postman](https://i.postimg.cc/NFmX4Kr2/SCR_20251120_pdhc.png)](https://postimg.cc/SYQjQNdm)

8. Click `Console` in the lower navigation ribbon to open it.
 
[![Console tab in Postman highlighted](https://i.postimg.cc/SRC9rnz6/SCR_20251124_oluo.png)](https://postimg.cc/wRx3xT2M)

9. The console now outputs the result you called for.
    
[![Console log results](https://i.postimg.cc/90Z9pDqT/SCR_20251120_pgca.png)](https://postimg.cc/YL2j2qRq)

Et voila!

## Objects vs. arrays
There's a difference in how you call on items in an object or an array. In the example above, the printed item is in an array. If you want to call on an item in an object instead, the notation to use is much simpler.
Using the example code above, here's how I'd call on different items in an object or array

| Item | Description | Type | Sample code |
| --- | --- | --- | --- |
| main | Weather type | array | content=responseData.weather[0].main |
| feels_like | Temperature | object | content=responseData.main.feels_like |
| speed | Wind speed | object | content=responseData.wind.speed |
| description | Weather description | array | content=responseData.weather[0].description |

## Conclusion
I hope this worked and helped you as much as it did for me. Enjoy!
