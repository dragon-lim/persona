from flask import Flask, render_template, request
from dotenv import load_dotenv
from anthropic import Anthropic

load_dotenv()

app = Flask(__name__)
client = Anthropic()

MODEL = "claude-sonnet-4-6"

# ==========================
# 은행 업무 매뉴얼 불러오기
# ==========================
with open("knowledge.txt", "r", encoding="utf-8") as f:
    KNOWLEDGE = f.read()

# ==========================
# 페르소나
# ==========================
SYSTEM_PROMPT = f"""당신은 '영진은행'의 고객 상담 챗봇입니다.

[당신의 역할] : 아래 업무 메뉴얼에 따라 고객의 질문에 대답 합니다

[당신의 말투] : 어떠한 질문이라도 친절하게 존댓말로 안내 해야합니다
                메뉴얼에 없거나 관련 질문이 아니여서, 답변을 하지 못하는 질문에는 '고객님 도움을 드리고 싶지만 제가 가능한 일이 아닙니다 죄송합니다.' 로 시작하여 사과만 드리면 됩니다.

[답변의 규칙] : 반드시 모든 답변은 아래 업무 메뉴얼의 내용을 바탕으로 답해야 합니다
                메뉴얼에 없는 내용은 절대 추측하면 안됩니다
                고객의 성함 및 계좌번호, 비밀번호 등 개인정보는 절대 물어보면 안됩니다
                고객의 개인정보에는 항상 '소중한 개인정보' 라고 말해야 합니다. 혹시나 고객이 개인정보를 먼저 알려주면 개인정보를 받은 것 같습니다 안내 말씀 드리자면 저희는 개인정보는 저장하지 않는다고 꼭 알려 드리며 고객에게 개인정보는 소중한 정보임으로 조심해야 한다는 점과, 도와드릴 수 없다고 안내해야 합니다

[업무 메뉴얼]
{KNOWLEDGE}"""

# 대화 기록 (단일 세션)
history = []


# ==========================
# 메인 화면
# ==========================
@app.route("/")
def index():
    return render_template("index.html")


# ==========================
# 채팅
# ==========================
@app.route("/chat", methods=["POST"])
def chat():

    data = request.json
    user_message = data["message"]

    history.append({"role": "user", "content": user_message})

    response = client.messages.create(
        model=MODEL,
        max_tokens=1024,
        system=SYSTEM_PROMPT,
        messages=history,
    )

    answer = response.content[0].text
    history.append({"role": "assistant", "content": answer})

    return {"answer": answer}


# ==========================
# 실행
# ==========================
if __name__ == "__main__":
    app.run(debug=True, port=5000)
