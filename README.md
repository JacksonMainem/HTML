<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Infix to Prefix Converter</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f2f2f2;
            padding: 30px;
        }
        input, button {
            width: 100%;
            padding: 10px;
            font-size: 16px;
            margin-top: 10px;
        }
        #result {
            margin-top: 15px;
            font-weight: bold;
        }
    </style>
</head>
<body>

<h2>Infix to Prefix Converter</h2>

<input type="text" id="infix" placeholder="Enter Infix Expression (e.g., A%(B+C))">
<button onclick="convert()">Convert</button>

<div id="result"></div>

<script>
    function precedence(c) {
        switch (c) {
            case '+':
            case '-': return 1;
            case '*':
            case '/':
            case '%': return 2; // Modulus same as multiply/divide
            case '^': return 3;
        }
        return -1;
    }

    function isOperator(c) {
        return "+-*/^%".includes(c);
    }

    // Validation
    function isValidInfix(exp) {
        let stack = [];
        let lastWasOperator = true;

        for (let c of exp) {
            if (c === ' ') continue;

            if (/[a-zA-Z0-9]/.test(c)) {
                lastWasOperator = false;
            }
            else if (c === '(') {
                stack.push(c);
                lastWasOperator = true;
            }
            else if (c === ')') {
                if (stack.length === 0) return false;
                stack.pop();
                lastWasOperator = false;
            }
            else if (isOperator(c)) {
                if (lastWasOperator) return false;
                lastWasOperator = true;
            }
            else {
                return false;
            }
        }
        return stack.length === 0 && !lastWasOperator;
    }

    // Infix → Postfix
    function infixToPostfix(exp) {
        let stack = [];
        let result = "";

        for (let c of exp) {
            if (/[a-zA-Z0-9]/.test(c)) {
                result += c;
            }
            else if (c === '(') {
                stack.push(c);
            }
            else if (c === ')') {
                while (stack.length && stack[stack.length - 1] !== '(')
                    result += stack.pop();
                stack.pop();
            }
            else if (isOperator(c)) {
                while (stack.length &&
                       precedence(c) <= precedence(stack[stack.length - 1])) {
                    result += stack.pop();
                }
                stack.push(c);
            }
        }

        while (stack.length)
            result += stack.pop();

        return result;
    }

    // Infix → Prefix
    function infixToPrefix(infix) {
        if (!isValidInfix(infix)) {
            return "❌ Invalid Infix Expression";
        }

        // Reverse infix
        let rev = infix.split('').reverse().join('');

        // Swap brackets
        rev = rev.replace(/\(/g, '#')
                 .replace(/\)/g, '(')
                 .replace(/#/g, ')');

        // Convert to postfix
        let postfix = infixToPostfix(rev);

        // Reverse postfix → prefix
        return postfix.split('').reverse().join('');
    }

    function convert() {
        let infix = document.getElementById("infix").value;
        let prefix = infixToPrefix(infix);
        document.getElementById("result").innerText =
            "Prefix Expression: " + prefix;
    }
</script>

</body>
</html>
