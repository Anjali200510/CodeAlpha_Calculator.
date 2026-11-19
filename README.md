const display = document.getElementById("display");
const buttons = document.querySelectorAll("button");

let expression = "";

function updateDisplay() {
    display.value = expression || "0";
}

function calculate() {
    if (!expression) return;

    try {
        // Allow only calculator characters
        if (!/^[0-9+\-*/%.() ]+$/.test(expression)) {
            throw new Error("Invalid input");
        }

        const result = Function(
            '"use strict"; return (' + expression + ')'
        )();

        if (!Number.isFinite(result)) {
            throw new Error("Invalid calculation");
        }

        expression = String(
            Number(result.toPrecision(12))
        );

        updateDisplay();
    } catch {
        display.value = "Error";
        expression = "";
    }
}

buttons.forEach(button => {
    button.addEventListener("click", () => {
        const value = button.dataset.value;
        const action = button.dataset.action;

        if (action === "clear") {
            expression = "";
        } 
        else if (action === "delete") {
            expression = expression.slice(0, -1);
        } 
        else if (action === "calculate") {
            calculate();
            return;
        } 
        else if (value) {
            expression += value;
        }

        updateDisplay();
    });
});

// Keyboard support
document.addEventListener("keydown", (event) => {
    const key = event.key;

    if (/^[0-9+\-*/%.()]$/.test(key)) {
        expression += key;
        updateDisplay();
    }
    else if (key === "Enter" || key === "=") {
        event.preventDefault();
        calculate();
    }
    else if (key === "Backspace") {
        expression = expression.slice(0, -1);
        updateDisplay();
    }
    else if (key === "Escape") {
        expression = "";
        updateDisplay();
    }
});
