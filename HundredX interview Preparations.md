## Questions to ask the interviewers
1. Ask about how many more interview rounds after this one
2. Talking to Alyinth, she had mentioned I would be a replacement for a previous engineer, would you mind telling what the engineer was doing/ working before he/she left
3. Is there anything challenging or anything you would like to change here at hundredx
4. What some tech/languages i should expect to be working on?
5. How does onboarding look? Is it peer programming for the foreseeable future, courses I need to take, documentation I need to read?


## create webpage that has a counter:
```
<html>
<head>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f4f8;
        }
        .container {
            text-align: center;
            padding: 20px;
            border: 1px solid #ccc;
            border-radius: 10px;
            background-color: #fff;
        }
        .counter {
            font-size: 2em;
            margin-bottom: 20px;
        }
        button {
            padding: 10px 20px;
            font-size: 1em;
            margin: 5px;
            cursor: pointer;
        }
        .clicks {
            font-size: 1.1em;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="counter">
            <p id="count">0</p>
        </div>
        <div>
            <button onclick="dec()">Decrement</button>
            <button onclick="inc()">Increment</button>
        </div>
        <div class="clicks">
            <p>Clicks on Increment: <span id="incCount">0</span></p>
            <p>Clicks on Decrement: <span id="decCount">0</span></p>
        </div>
    </div>
    <script>
        let c = 0, ci = 0, cd = 0;
        const count = document.getElementById("count");
        const incCount = document.getElementById("incCount");
        const decCount = document.getElementById("decCount");
        function inc() {
            c++;
            ci = (ci >= 10) ? 0 : ci + 1;
            update();
        }
        function dec() {
            c = c > 0 ? c - 1 : 0;
            cd = (cd >= 10) ? 0 : cd + 1;
            update();
        }
        function update() {
            count.textContent = c;
            incCount.textContent = ci;
            decCount.textContent = cd;
        }
    </script>
</body>
</html>
```