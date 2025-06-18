<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Faykur Mega Unit Converter</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@600&display=swap');

  body {
    font-family: Arial, sans-serif;
    background: #f0f4f7;
    margin: 0; padding: 30px;
    display: flex;
    flex-direction: column;
    align-items: center;
    min-height: 100vh;
    box-sizing: border-box;
  }
  header {
    width: 100%;
    max-width: 960px;
    display: flex;
    justify-content: flex-end;
    margin-bottom: 20px;
  }
  #logo {
    font-family: 'Poppins', sans-serif;
    font-weight: 600;
    font-size: 2.2rem;
    color: #0a74da;
    user-select: text;
    letter-spacing: 2px;
    text-shadow:
      0 0 5px #0a74da,
      0 0 10px #0a74da,
      0 0 20px #0a74da,
      0 0 40px #0a74da;
    cursor: default;
  }
  h1 {
    margin: 0 0 20px 0;
    font-size: 2.5rem;
    color: #333;
  }
  #categoryButtons {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    max-width: 960px;
    gap: 12px;
    margin-bottom: 30px;
  }
  button.category-btn {
    background: #0a74da;
    color: white;
    border: none;
    border-radius: 6px;
    padding: 15px 24px;
    font-size: 1.1rem;
    font-weight: 600;
    cursor: pointer;
    min-width: 130px;
    transition: background-color 0.3s ease;
  }
  button.category-btn:hover,
  button.category-btn.active {
    background: #084c8a;
  }
  #converter {
    background: white;
    box-shadow: 0 4px 12px rgb(0 0 0 / 0.1);
    border-radius: 10px;
    padding: 30px 40px 40px;
    max-width: 480px;
    width: 100%;
    box-sizing: border-box;
  }
  input[type="number"], select {
    width: 100%;
    font-size: 1.2rem;
    padding: 12px 14px;
    margin: 12px 0 24px;
    border-radius: 6px;
    border: 1.5px solid #ddd;
    box-sizing: border-box;
    transition: border-color 0.25s ease;
  }
  input[type="number"]:focus, select:focus {
    border-color: #0a74da;
    outline: none;
  }
  button.convert-btn {
    width: 100%;
    background: #0a74da;
    color: white;
    font-size: 1.3rem;
    font-weight: 700;
    padding: 14px 0;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    margin-top: 8px;
    transition: background-color 0.3s ease;
  }
  button.convert-btn:hover {
    background: #084c8a;
  }
  #result {
    margin-top: 30px;
    font-size: 1.4rem;
    font-weight: 600;
    color: #222;
    min-height: 40px;
  }
  @media (max-width: 520px) {
    #converter {
      padding: 20px 20px 30px;
    }
    button.category-btn {
      min-width: 100px;
      padding: 12px 16px;
      font-size: 1rem;
    }
  }
</style>
</head>
<body>
<header>
  <div id="logo">Faykur</div>
</header>
<h1>Mega Unit Converter</h1>

<div id="categoryButtons"></div>

<div id="converter">
  <input type="number" id="value" placeholder="Enter value" />
  <select id="fromUnit"><option value="">Convert from</option></select>
  <select id="toUnit"><option value="">Convert to</option></select>
  <button class="convert-btn" onclick="convert()">Convert</button>
  <div id="result"></div>
</div>

<script>
  // Expanded conversion data per category
  const conversionData = {
    Length: {
      units: {
        "Meter": 1,
        "Kilometer": 1000,
        "Centimeter": 0.01,
        "Millimeter": 0.001,
        "Micrometer": 0.000001,
        "Nanometer": 0.000000001,
        "Mile": 1609.344,
        "Yard": 0.9144,
        "Foot": 0.3048,
        "Inch": 0.0254,
        "Nautical Mile": 1852
      },
      baseUnit: "Meter",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Temperature: {
      units: ["Celsius", "Fahrenheit", "Kelvin"],
      convert: function(value, from, to) {
        if (from === to) return value;
        let celsius;

        // Convert from source to Celsius
        switch(from) {
          case "Celsius": celsius = value; break;
          case "Fahrenheit": celsius = (value - 32) * 5/9; break;
          case "Kelvin": celsius = value - 273.15; break;
          default: return null;
        }
        // Celsius to target
        switch(to) {
          case "Celsius": return celsius;
          case "Fahrenheit": return celsius * 9/5 + 32;
          case "Kelvin": return celsius + 273.15;
          default: return null;
        }
      }
    },

    Area: {
      units: {
        "Square meter": 1,
        "Square kilometer": 1e6,
        "Square centimeter": 0.0001,
        "Square millimeter": 0.000001,
        "Hectare": 10000,
        "Acre": 4046.8564224,
        "Square mile": 2.59e6,
        "Square yard": 0.83612736,
        "Square foot": 0.09290304,
        "Square inch": 0.00064516
      },
      baseUnit: "Square meter",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Volume: {
      units: {
        "Cubic meter": 1,
        "Liter": 0.001,
        "Milliliter": 0.000001,
        "Cubic centimeter": 0.000001,
        "Cubic inch": 0.0000163871,
        "Cubic foot": 0.0283168,
        "Cubic yard": 0.764555,
        "US gallon": 0.00378541,
        "US quart": 0.000946353,
        "US pint": 0.000473176,
        "US cup": 0.000236588,
        "US fluid ounce": 0.0000295735,
        "US tablespoon": 0.0000147868,
        "US teaspoon": 0.00000492892,
        "Imperial gallon": 0.00454609,
        "Imperial quart": 0.00113652,
        "Imperial pint": 0.000568261,
        "Imperial fluid ounce": 0.0000284131
      },
      baseUnit: "Cubic meter",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Weight: {
      units: {
        "Kilogram": 1,
        "Gram": 0.001,
        "Milligram": 0.000001,
        "Metric ton": 1000,
        "Long ton": 1016.0469088,
        "Short ton": 907.18474,
        "Pound": 0.45359237,
        "Ounce": 0.0283495231,
        "Carat": 0.0002,
        "Atomic mass unit": 1.6605390666e-27
      },
      baseUnit: "Kilogram",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Time: {
      units: {
        "Second": 1,
        "Millisecond": 0.001,
        "Microsecond": 0.000001,
        "Nanosecond": 0.000000001,
        "Minute": 60,
        "Hour": 3600,
        "Day": 86400,
        "Week": 604800,
        "Month (30 days)": 2592000,
        "Year (365 days)": 31536000,
        "Decade": 315360000,
        "Century": 3153600000
      },
      baseUnit: "Second",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Speed: {
      units: {
        "Meters per second": 1,
        "Kilometers per hour": 0.277778,
        "Miles per hour": 0.44704,
        "Foot per second": 0.3048,
        "Knot": 0.514444
      },
      baseUnit: "Meters per second",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    DataStorage: {
      units: {
        "Bit": 1,
        "Kilobit": 1024,
        "Megabit": 1048576,
        "Gigabit": 1073741824,
        "Terabit": 1099511627776,
        "Petabit": 1125899906842624,
        "Byte": 8,
        "Kilobyte": 8192,
        "Megabyte": 8388608,
        "Gigabyte": 8589934592,
        "Terabyte": 8796093022208,
        "Petabyte": 9007199254740992
      },
      baseUnit: "Bit",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Energy: {
      units: {
        "Joule": 1,
        "Kilojoule": 1000,
        "Calorie (thermo)": 4184,
        "Calorie (IT)": 4.1868,
        "Kilocalorie": 4184,
        "Watt hour": 3600,
        "Kilowatt hour": 3600000,
        "Electronvolt": 1.60218e-19,
        "British thermal unit": 1055.06
      },
      baseUnit: "Joule",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Pressure: {
      units: {
        "Pascal": 1,
        "Kilopascal": 1000,
        "Bar": 100000,
        "Atmosphere": 101325,
        "Torr": 133.322,
        "Pound per square inch": 6894.76
      },
      baseUnit: "Pascal",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Power: {
      units: {
        "Watt": 1,
        "Kilowatt": 1000,
        "Megawatt": 1e6,
        "Horsepower": 745.7
      },
      baseUnit: "Watt",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Angle: {
      units: {
        "Degree": 1,
        "Radian": 57.2958,
        "Grad": 0.9,
        "Minute of arc": 1/60,
        "Second of arc": 1/3600
      },
      baseUnit: "Degree",
      convert: function(value, from, to) {
        return value * this.units[from] / this.units[to];
      }
    },

    Currency: {
      units: {
        "USD": 1,
        "EUR": 0.93,
        "GBP": 0.82,
        "JPY": 141.04,
        "CAD": 1.35,
        "AUD": 1.46,
        "CHF": 0.91,
        "CNY": 6.92,
        "INR": 82.31,
        "RUB": 76.89,
        "SAR": 3.75
      },
      baseUnit: "USD",
      convert: function(value, from, to) {
        return value * this.units[to] / this.units[from];
      }
    }
  };

  // Global state
  const categories = Object.keys(conversionData);
  const categoryButtonsContainer = document.getElementById('categoryButtons');
  const fromUnitSelect = document.getElementById('fromUnit');
  const toUnitSelect = document.getElementById('toUnit');
  const resultDiv = document.getElementById('result');
  const valueInput = document.getElementById('value');

  let currentCategory = categories[0]; // default category

  // Create category buttons
  function setupCategoryButtons() {
    categories.forEach(cat => {
      const btn = document.createElement('button');
      btn.textContent = cat;
      btn.className = 'category-btn';
      if (cat === currentCategory) btn.classList.add('active');
      btn.addEventListener('click', () => {
        if (currentCategory !== cat) {
          currentCategory = cat;
          updateCategoryUI();
        }
      });
      categoryButtonsContainer.appendChild(btn);
    });
  }

  // Update buttons active state
  function updateButtonsActiveState() {
    [...categoryButtonsContainer.children].forEach(btn => {
      btn.classList.toggle('active', btn.textContent === currentCategory);
    });
  }

  // Populate the from/to selects with units of the current category
  function populateUnitSelectors() {
    const catData = conversionData[currentCategory];
    fromUnitSelect.innerHTML = '<option value="">Convert from</option>';
    toUnitSelect.innerHTML = '<option value="">Convert to</option>';

    if (currentCategory === "Temperature") {
      catData.units.forEach(unit => {
        const optionFrom = document.createElement('option');
        optionFrom.value = unit;
        optionFrom.textContent = unit;
        fromUnitSelect.appendChild(optionFrom);

        const optionTo = document.createElement('option');
        optionTo.value = unit;
        optionTo.textContent = unit;
        toUnitSelect.appendChild(optionTo);
      });
    } else {
      for (const unit in catData.units) {
        const optionFrom = document.createElement('option');
        optionFrom.value = unit;
        optionFrom.textContent = unit;
        fromUnitSelect.appendChild(optionFrom);

        const optionTo = document.createElement('option');
        optionTo.value = unit;
        optionTo.textContent = unit;
        toUnitSelect.appendChild(optionTo);
      }
    }
  }

  // Update the UI on category change
  function updateCategoryUI() {
    updateButtonsActiveState();
    populateUnitSelectors();
    resultDiv.textContent = "";
    valueInput.value = "";
  }

  // Main convert function
  function convert() {
    const value = parseFloat(valueInput.value);
    const from = fromUnitSelect.value;
    const to = toUnitSelect.value;

    if (isNaN(value)) {
      resultDiv.textContent = "Please enter a valid number.";
      return;
    }
    if (!from || !to) {
      resultDiv.textContent = "Please select both units.";
      return;
    }
    if (from === to) {
      resultDiv.textContent = `${value} ${from} = ${value} ${to}`;
      return;
    }

    const catData = conversionData[currentCategory];
    let converted;

    try {
      if (currentCategory === "Temperature") {
        converted = catData.convert(value, from, to);
      } else if (currentCategory === "Currency") {
        converted = catData.convert(value, from, to);
      } else {
        converted = catData.convert(value, from, to);
      }
    } catch {
      resultDiv.textContent = "Conversion error.";
      return;
    }

    if (converted === null || isNaN(converted)) {
      resultDiv.textContent = "Conversion not supported.";
      return;
    }

    // Format number with max 6 decimals, remove trailing zeros
    const formatted = +converted.toFixed(6);

    resultDiv.textContent = `${value} ${from} = ${formatted} ${to}`;
  }

  // Initialization
  setupCategoryButtons();
  updateCategoryUI();

</script>
</body>
</html>
