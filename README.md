<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حساب استهلاك الكهرباء والشريحة</title>
</head>
<body style="font-family: Arial, sans-serif; direction: rtl; text-align: right;">

    <h2>حاسبة استهلاك الكهرباء</h2>

    <label for="chargedAmount">كم قمت بشحنه بالجنيه المصري:</label>
    <input type="number" id="chargedAmount" placeholder="مثال: 200" required><br><br>

    <label for="remainingAmount">كم تبقى في العداد بالجنيه المصري:</label>
    <input type="number" id="remainingAmount" placeholder="مثال: 50" required><br><br>

    <button onclick="calculateConsumption()">احسب الاستهلاك</button>

    <h3 id="result"></h3>

    <script>
        function calculateConsumption() {
            // تعيين الشرائح وأسعارها
            const tariffs = [
                { maxUsage: 50, rate: 0.68 },
                { maxUsage: 100, rate: 0.78 },
                { maxUsage: 200, rate: 0.95 },
                { maxUsage: 350, rate: 1.55 },
                { maxUsage: 650, rate: 1.95 },
                { maxUsage: 1000, rate: 2.10 },
                { maxUsage: Infinity, rate: 2.23 }
            ];

            // استرداد القيم المدخلة
            const chargedAmount = parseFloat(document.getElementById("chargedAmount").value);
            const remainingAmount = parseFloat(document.getElementById("remainingAmount").value);

            if (isNaN(chargedAmount) || isNaN(remainingAmount) || chargedAmount <= remainingAmount) {
                document.getElementById("result").innerText = "يرجى إدخال القيم بشكل صحيح.";
                return;
            }

            // حساب الاستهلاك بالجنيه
            const usedAmount = chargedAmount - remainingAmount;

            // تحويل الاستهلاك إلى كيلووات
            let consumptionKWh = 0;
            let remainingUsage = usedAmount;

            for (let i = 0; i < tariffs.length; i++) {
                const { maxUsage, rate } = tariffs[i];
                const rangeUsage = maxUsage - (i > 0 ? tariffs[i - 1].maxUsage : 0);
                const rangeCost = rangeUsage * rate;

                if (remainingUsage > rangeCost) {
                    consumptionKWh += rangeUsage;
                    remainingUsage -= rangeCost;
                } else {
                    consumptionKWh += remainingUsage / rate;
                    break;
                }
            }

            // تحديد الشريحة النهائية
            let tariffLevel;
            for (let i = 0; i < tariffs.length; i++) {
                if (consumptionKWh <= tariffs[i].maxUsage) {
                    tariffLevel = i + 1;
                    break;
                }
            }

            // عرض النتائج
            document.getElementById("result").innerText = 
                `استهلكت: ${consumptionKWh.toFixed(2)} كيلو وات\n` +
                `التكلفة: ${usedAmount.toFixed(2)} جنيه مصري\n` +
                `الشريحة الحالية: ${tariffLevel}`;
        }
    </script>

</body>
</html>
