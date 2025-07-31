from flask import Flask, render_template, request
import subprocess
import os
import requests

app = Flask(__name__)

WOLFRAM_APPID = "6JP9U2AAW4"  # Replace with your actual App ID
QALCULATE_PATH = "/usr/bin/qalc"  # Won’t work on Replit; works only where qalc is installed

def solve_qalc(expression: str) -> str:
    try:
        result = subprocess.run(
            [QALCULATE_PATH, expression],
            capture_output=True,
            text=True,
            input="y"
        )
        return result.stdout.strip()
    except Exception as e:
        return f"[Qalculate Error] {str(e)}"

def solve_wolfram(expression: str) -> str:
    try:
        url = "http://api.wolframalpha.com/v2/query"
        params = {
            "input": expression,
            "appid": WOLFRAM_APPID,
            "output": "JSON"
        }
        response = requests.get(url, params=params)
        data = response.json()
        pods = data.get("queryresult", {}).get("pods", [])
        for pod in pods:
            if pod["title"].lower() in ["result", "solution", "exact result", "definite integral"]:
                return pod["subpods"][0]["plaintext"]
        if pods:
            return pods[0]["subpods"][0]["plaintext"]
        else:
            return "[Wolfram] No answer found."
    except Exception as e:
        return f"[Wolfram Error] {str(e)}"

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/calculate", methods=["POST"])
def calculate():
    expression = request.form.get("expression", "")
    result = solve_qalc(expression)
    return render_template("calculate.html", expression=expression, result=result)

@app.route("/search", methods=["POST"])
def search():
    query = request.form.get("query", "")
    result = solve_wolfram(query)
    return render_template("search.html", query=query, result=result)

@app.route("/game")
def game():
    return render_template("game.html")

if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0", port=8080)
