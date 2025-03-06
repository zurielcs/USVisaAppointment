# Appointment Availability Instructions

This guide provides step-by-step instructions to access the U.S. Visa appointment system, sign in, and run a JavaScript script in the browser's Developer Tools to recover available appointment dates.

## Prerequisites
- Google Chrome or any modern browser with Developer Tools (F12)
- Active user account on [ais.usvisa-info.com](https://ais.usvisa-info.com/en-mx/niv/users/sign_in)
- An active visa application with status "Schedule Appointment"
- Basic knowledge of browser developer tools

## Steps

### 1. Access the Website
Go to the following link:
[https://ais.usvisa-info.com/en-mx/niv/users/sign_in](https://ais.usvisa-info.com/en-mx/niv/users/sign_in)

### 2. Sign In
- Enter your registered email and password.
- Complete the CAPTCHA verification.
- Click **Sign In**.

### 3. Open Developer Tools
- Right-click anywhere on the page and select **Inspect** (or press **F12** on Windows or **Cmd + Option + I** on Mac).
- Go to the **Console** tab in Developer Tools.

### 4. Run JavaScript Script
- Copy and paste the following JavaScript code into the console:

```javascript
$(document).ready(function () {
    let limit_appointments = 10;
    let cities = [[65, 'Ciudad Juarez'], [66, 'Guadalajara'], [67, 'Hermosillo'], [68, 'Matamoros'], [69, 'Merida'], [70, 'Mexico City'], [71, 'Monterrey'], [72, 'Nogales'], [73, 'Nuevo Laredo'], [74, 'Tijuana']];

    function getCityData(city) {
        let apiUrl = 'https://ais.usvisa-info.com/en-mx/niv/schedule/' + groupId + '/appointment/days/' + city[0] + '.json?appointments[expedite]=false';
        $.ajax({
            url: apiUrl,
            type: 'GET',
            dataType: 'json',
            success: function (response) {
                //console.log(response);
                if (response.length == 0) {
                    console.log(city[1] + ' no appointments');
                } else {
                    console.info(city[1] + ':');
                    for (let i = 0; i < limit_appointments; i++) {
                        console.info(response[i].date);
                    }
                }
            },
            error: function (xhr, status, error) {
                console.error('API Request Failed:', error);
            }
        });
    }

    function findMatch(text) {
        let match = text.match(/\/schedule\/(\d+)\/.+/);
        if (match) {
            let extractedNumber = match[1];
            return extractedNumber;
        }
    }

    function findGroupId() {
        let currentUrl = document.URL;
        let match = findMatch(currentUrl);
        if (match) {
            return match;
        } else {
            let status = $(".status:contains('Schedule Appointment')");
            if (status.length == 0) {
                status = $(".status:contains('Attend Appointment')");
            }
            let match = findMatch(status.parent().parent().html());
            return match;
        }
    }

    let groupId = findGroupId();

    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    async function start() {
        for (let i = 0; i < cities.length; i++) {
            getCityData(cities[i]);
            await sleep(4000);
        }
        console.log('Done');
    }

    start();
});
```

### 5. Notes

- You can modify the `limit_appointments` variable to set a maximum number of appointment dates to display.
- Customize the `cities` variable to filter appointments by specific cities as desired.

### 6. View Results
- Press **Enter** to execute the script.
- The available appointment dates will be printed in the console.


### 7. Disclaimer
This method is for educational purposes only. Use it responsibly and in compliance with the website's terms of service.

****
