from fastapi import FastAPI, Request
import requests

app = FastAPI()
from dotenv import dotenv_values

API_KEY = dotenv_values(".env") ['IP_BASE_API_KEY']

@app.get("/")
async def hello():
    return {"sunny":"yesterday"}

@app.get("/api/hello")
async def root(request:Request):
    try:
        if request.method=="GET":
            # visitor_name = requests.args.get('visitor_name')

            ip_address= request.client.host
            temperature=0
            city=""
            
            response = requests.get(f"https://api.ip2location.io/?key=0879B8DA7DDD15CD41D5EAC185448C21&ip={ip_address}")
            return {
                "response":response.json()
            }

            if response.status_code == 200:
                data = response.json()
                city  = data['city_name']
                long = data['longitude']
                latitude = data['latitude']

                print("city", city)
                print("long", long)
                print("latitude", latitude)

                return {
                    "city":city,
                    "longitude": long,
                    "latitude":latitude
                }


            # url= f'https://api.ipbase.com/v1/json/{ip_address}'

            # """
            #     <a href="https://onelink.to/acbu5m">Download</a>`
            # """
            # response = requests.get(url)

            # if response.status_code == 200:
            #     data = response.json()
            #     temperature = get_temperature(data.get('latitude'),data.get('longitude'), API_KEY)

            #     city = "Lagos"
            #     result = {
            #         "client_ip": ip_address,
            #         "location":city,
            #         "greetings":f"Hello,{visitor_name}!, the temperature is {temperature} degrees Celsius in {city}",
            #     }
            #     return {"result":result}
            else:
                return {'message':"The api failed to fetch"}

    except ConnectionError:
        print(ConnectionError)
    except EnvironmentError:
        print(EnvironmentError)


def get_temperature(latitude, longitude, api_key):
    try:
        api= f"https://api.openweathermap.org/data/2.5/weather?lat={latitude}&lon={longitude}&appid={api_key}"
        response = requests.get(api)
        temperature =""

        if response.status_code == 200:
            data = response.json()
            temperature =  calculate_temperature(int(data.get('main')['temp']))
        
        return temperature
    except ConnectionError as e:
        raise e

def calculate_temperature(f):
    return int(float(((f-32) * 5)/9))



if __name__ == '__main__':
    app.run(host="0.0.0.0", debug=True, port=5000)
