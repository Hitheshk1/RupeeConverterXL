# Indian Currency Words Converter

This repository provides an Excel formula designed to convert Indian Rupee amounts into words, covering denominations from paise to kharabs. The formula follows the Indian numbering system, which includes units like lakhs, crores, arabs, and kharabs.

Ideal for use in financial documents, invoices, or any other context where Indian currency amounts need to be written out in words.

## Features
- Converts Indian Rupees (₹) and paise to words.
- Supports amounts ranging from paise up to kharabs.
- Handles large numbers, such as up to ₹99,99,99,99,999 (99.9 Kharabs).
- Fully compatible with Microsoft Excel.

## How It Works
The formula:
- Breaks down the given amount into parts based on its scale (Thousands, Lakhs, Crores, Arabs, and Kharabs).
- Converts each part of the number into words using predefined mappings for units, tens, teens, and scales.
- Outputs the result in the format "Rupees [Amount] Only", with an optional Paise part if any decimal exists.

## How to Use
1. **Input a numeric value** (₹) in the designated cell (e.g., `G23`).
2. Copy the following formula into the cell where you want the output to appear:
3. The formula will output the currency in words format such as:
  "Rupees One Thousand Two Hundred Thirty-Four and Fifty-Six Paise Only"
  "Rupees Ten Lakh Only"
  "Rupees and Fifty Paise Only"
 
 ##Example Usage
  Input: 56,789.45

  Output: Rupees Fifty-Six Thousand Seven Hundred Eighty-Nine and Forty-Five Paise Only

  Input: 1,23,45,67,890.12

  Output: Rupees One Arab Twenty-Three Crore Forty-Five Lakh Sixty-Seven Thousand Eight Hundred Ninety and Twelve Paise Only


Contributing
If you'd like to contribute to this repository, feel free to submit a pull request. Suggestions and improvements are always welcome!

License
This project is licensed under the MIT License - see the LICENSE file for details.


```excel
=LET(
    num, FLOOR(G23, 1),
    decimalPart, ROUND(MOD(G23, 1) * 100, 0),
    unitsMap, {"", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine"},
    teensMap, {"Ten", "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen", "Eighteen", "Nineteen"},
    tensMap, {"", "", "Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", "Eighty", "Ninety"},
    scalesMap, {"", "Thousand", "Lakh", "Crore", "Arab", "Kharab"},

    getWords, LAMBDA(n,
        IF(n = 0, "",
            IF(n < 10, INDEX(unitsMap, n + 1),
                IF(n < 20, INDEX(teensMap, n - 9),
                    TRIM(CONCAT(
                        INDEX(tensMap, INT(n / 10) + 1),
                        IF(MOD(n, 10) > 0, CONCAT(" ", INDEX(unitsMap, MOD(n, 10) + 1)), "")
                    ))
                )
            )
        )
    ),

    getScaleWords, LAMBDA(n, scale,
        IF(n = 0, "",
            TRIM(CONCAT(
                IF(INT(n / 100) > 0, CONCAT(getWords(INT(n / 100)), " Hundred "), ""),
                getWords(MOD(n, 100)),
                IF(n > 0, CONCAT(" ", INDEX(scalesMap, scale + 1)), "")
            ))
        )
    ),

    kharabPart, INT(num / 1000000000000),
    arabPart, INT(MOD(num, 1000000000000) / 10000000000),
    crorePart, INT(MOD(num, 10000000000) / 10000000),
    lakhPart, INT(MOD(num, 10000000) / 100000),
    thousandPart, INT(MOD(num, 100000) / 1000),
    hundredPart, MOD(num, 1000),

    rupeesWords, TEXTJOIN(" ", TRUE,
        getScaleWords(kharabPart, 5),
        getScaleWords(arabPart, 4),
        getScaleWords(crorePart, 3),
        getScaleWords(lakhPart, 2),
        getScaleWords(thousandPart, 1),
        getScaleWords(hundredPart, 0)
    ),

    paiseWords, IF(decimalPart > 0,
        CONCAT(" and ", getWords(decimalPart), " Paise"),
        ""
    ),

    result, IF(num > 0,
        CONCAT("Rupees ", TRIM(rupeesWords), paiseWords, " Only"),
        IF(decimalPart > 0, CONCAT("Rupees", paiseWords, " Only"), "-nil-")
    ),

    result
)
