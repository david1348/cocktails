# cocktails

import os
import requests
from flask import Flask, render_template

app = Flask(__name__)

API_URL = "https://boozeapi.com/api/v1/cocktails"
IMAGE_FOLDER = "static/images"

os.makedirs(IMAGE_FOLDER, exist_ok=True)

@app.route("/")
def index():
    response = requests.get(API_URL)

    drinks = []

    if response.status_code == 200:
        data = response.json()

        for drink in data:
            name = drink.get("name")
            image_url = drink.get("image")

            if name and image_url:
                image_name = image_url.split("/")[-1]
                image_path = os.path.join(IMAGE_FOLDER, image_name)

                # stiahnutie obrázka
                if not os.path.exists(image_path):
                    img_data = requests.get(image_url).content
                    with open(image_path, "wb") as f:
                        f.write(img_data)

                drinks.append({
                    "name": name,
                    "image": image_name
                })

    return render_template("index.html", drinks=drinks)


if __name__ == "__main__":
    app.run(debug=True)
