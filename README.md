<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>사명자 실력점검 시험 대비 모의고사</title>
  <style>
    :root {
      --primary: #2563eb;
      --bg: #f8fafc;
      --card-bg: #ffffff;
      --text: #1e293b;
      --correct: #16a34a;
      --wrong: #dc2626;
    }
    body { font-family: 'Apple SD Gothic Neo', sans-serif; background-color: var(--bg); color: var(--text); margin: 0; padding: 20px; line-height: 1.5; }
    .container { max-width: 800px; margin: 0 auto; background: var(--card-bg); padding: 30px; border-radius: 12px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
    h1 { text-align: center; color: #0f172a; margin-bottom: 20px; font-size: 1.8rem; }
    .progress-bar { font-weight: bold; text-align: right; color: var(--primary); margin-bottom: 15px; font-size: 1.1rem; }
    .question-card { border: 1px solid #e2e8f0; padding: 20px; border-radius: 8px; margin-bottom: 20px; background: #fff; }
    .question-title { font-weight: bold; font-size: 1.05rem; margin-bottom: 8px; white-space: pre-line; }
    .source-text { font-size: 0.85rem; color: #64748b; margin-bottom: 12px; }
    .badge { display: inline-block; padding: 3px 8px; font-size: 0.8rem; border-radius: 4px; color: white; margin-right: 6px; font-weight: normal; }
    .badge-normal { background: #64748b; }
    .badge-bible { background: #8b5cf6; }
    .badge-jss { background: #f59e0b; }
    
    textarea, input[type="text"] { width: 100%; padding: 10px; border: 1px solid #cbd5e1; border-radius: 6px; box-sizing: border-box; font-size: 1rem; font-family: inherit; }
    textarea { resize: vertical; min-height: 90px; margin-top: 8px; }
    .sub-input-row { display: flex; align-items: center; margin-top: 8px; gap: 8px; }
    .sub-label { font-weight: bold; color: var(--primary); font-size: 1.0rem; min-width: 36px; }
    .sub-input-row input { flex: 1; } 

    .nav-buttons { display: flex; justify-content: space-between; margin-top: 20px; }
    button { padding: 12px 24px; font-size: 1rem; font-weight: bold; border: none; border-radius: 6px; cursor: pointer; transition: 0.2s; }
    .btn-prev { background: #94a3b8; color: white; }
    .btn-next { background: var(--primary); color: white; }
    .btn-submit { background: #16a34a; color: white; display: none; }
    .btn-retry { background: #0284c7; color: white; display: block; margin: 20px auto 0 auto; } 
    
    .result-mark { font-size: 1.3rem; margin-left: 8px; }
    .mark-correct { color: var(--correct); }
    .mark-wrong { color: var(--wrong); }
    .feedback-box { background: #f1f5f9; padding: 12px; border-radius: 6px; margin-top: 10px; font-size: 0.95rem; white-space: pre-line; }
    .diff-correct { color: var(--correct); font-weight: bold; }
    .score-banner { text-align: center; font-size: 1.5rem; font-weight: bold; padding: 15px; background: #e0f2fe; border-radius: 8px; margin-bottom: 20px; color: #0369a1; }
  </style>
</head>
<body>

<div class="container">
  <h1>📖 사명자 실력점검 시험 대비 모의고사</h1>
  <div id="quiz-container">
    <div id="progress" class="progress-bar">Page 1 / 5</div>
    <div id="questions-area"></div>
    <div class="nav-buttons">
      <button id="btn-prev" class="btn-prev" onclick="changePage(-1)">이전 페이지</button>
      <button id="btn-next" class="btn-next" onclick="changePage(1)">다음 페이지</button>
      <button id="btn-submit" class="btn-submit" onclick="submitExam()">최종 제출하기</button>
    </div>
  </div>
  
  <div id="result-area" style="display:none;">
    <div id="score-banner" class="score-banner"></div>
    <div id="result-list"></div>
    <button class="btn-retry" onclick="initExam()">🔄 다시 풀기 (새로운 문제 조합)</button>
  </div>
</div>

<script>
const circNums = ['①', '②', '③', '④', '⑤'];

const bankNormal = [
  {
    q: "나는 성경이 말씀한 누구인지, 관련 성경 장과 함께 쓰시오.",
    keywords: [
      "계시록 성취 때", "하나님의 씨", "추수", "인", "계7장", "소속", "계14장", "실체",
      ["12지파", "열두지파"],
      ["첫 열매", "처음 익은 열매"]
    ],
    a: "나는 계시록 성취 때 하나님의 씨로 나서 추수되고 인 맞아 계 7장의 12지파에 소속된 계 14장의 첫 열매의 실체이다."
  },
  {
    q: "내가 참으로 이 성경대로 창조되었다는 증거는 무엇인가?",
    keywords: [
      "추수", "인", "계 7장", "소속",
      ["12지파", "열두지파"]
    ],
    a: "추수되고 인 맞아 계 7장의 12지파에 소속된 것"
  },
  {
    q: "성경에는 생명나무와 선악을 알게 하는 나무가 있다. 오늘날 ①생명나무 과실과 ②선악나무 과실은 무엇인지 각각 쓰시오.",
    keywords: [
      ["계시록", "실상", "말씀"],
      ["거짓 목자", "거짓 교리"]
    ],
    a: [
      "계시록의 실상의 말씀",
      "거짓 목자의 거짓 교리(주석)"
    ]
  },
  {
    q: "하나님이 주시는 두 가지의 계시는 ①무엇이고, 그중 계시록 성취 때 하나님의 종들이 받아야 할 계시는 ②무엇이며, 이 계시가 ③전달되는 과정을 쓰시오.",
    a: [
      "예언적 환상 계시, 실상 계시",
      "실상 계시",
      "하나님 -> 예수님 -> 천사 -> 새요한 -> 종들"
    ]
  },
  {
    q: "계 1:3에 ①‘읽는 자’와 ②‘듣는 자들’과 ③‘지키는 자들’은 누구인지 각각 계시록의 해당 장(성구)과 함께 쓰시오.",
    keywords: [
      ["열린 책", "새요한", "계10장"],
      ["새요한", "배우는", "백성", "나라", "방언", "임금", "계10:11"],
      ["새요한", "12지파", "144,000", "흰 무리", "계7장"]
    ],
    a: [
      "열린 책을 받아먹은 새요한 (계 10장)",
      "새요한에게 배우는 백성, 나라, 방언, 임금 (계 10:11)",
      "새요한을 포함한 12지파 144,000과 흰 무리 (계 7장)"
    ]
  },
  {
    q: "계시록 성취는 ①누구의 출현으로 시작되는지 관련 성구와 함께 쓰고, 계시록이 성취되기 시작한 이 일은 ②언제, ③어디에서 실상으로 이루어졌는지 쓰시오.",
    keywords: [
      ["재림 예수님", "계1, 2, 3장"],
      ["1966"],
      ["과천", "청계산", "장막성전"]
    ],
    a: [
      "재림 예수님 (계 1, 2, 3장)",
      "1966년",
      "과천 소재 청계산 하(下) 장막성전"
    ]
  },
  {
    q: "계 2~3장에 보낸 편지 내용은 ①몇 가지 무엇이며, 계 2~3장의 출현 인물 ②세 존재를 순차적으로 쓰고, 그들이 각자 ③한 일을 쓰시오.",
    labels: ["①", "②", "③ 일곱 사자", "   니골라당", "   요한"],
    a: [
      "세 가지: 본 것, 이제 있는 일, 장차 될 일",
      "일곱 사자, 니골라당, 요한",
      "길 예비 등불 역사",
      "우상의 제물과 교훈, 행음",
      "회개할 것과 약속의 말씀 편지"
    ]
  },
  {
    q: "계시록의 3가지 비밀은 무엇인지 성구와 함께 기록하시오.",
    keywords: [
      ["일곱 별", "일곱 금 촛대", "계1:20"],
      ["음녀", "바벨론", "일곱 머리", "열 뿔", "짐승", "계17:5"],
      ["일곱째 나팔", "계10:7"]
    ],
    a: [
      "일곱 별과 일곱 금 촛대의 비밀 (계 1:20)",
      "음녀 바벨론과 일곱 머리와 열 뿔 가진 짐승의 비밀 (계 17:5~7)",
      "마지막 일곱째 나팔의 비밀 (계 10:7)"
    ]
  },
  {
    q: "이기는 자에게 주겠다고 계 2~3장에 약속한 ①복을 쓰고, 이것 외에 이기는 자가 받는 ②또 1가지 복을 증거 성구와 함께 쓰시오.",
    keywords: [
      ["생명나무 과실", "생명의 면류관", "둘째 사망의 해 면함", "감추었던 만나", "흰 돌", "철장", "새벽별", "흰옷", "생명책 녹명", "하나님 성전의 기둥", "하나님과 거룩한 성 새 예루살렘의 이름과 예수님의 새 이름", "예수님의 보좌"],
      ["유업", "아들", "계21:7"]
    ],
    a: [
      "생명나무 과실, 생명의 면류관, 둘째 사망의 해를 면함, 감추었던 만나, 흰 돌, 철장, 새벽별, 흰옷 입음, 생명책 녹명, 하나님 성전의 기둥, 하나님과 거룩한 성 새 예루살렘의 이름과 예수님의 새 이름 기록, 예수님의 보좌에 앉게 해 줌",
      "유업을 받는 아들이 됨 (계 21:7)"
    ]
  },
  {
    q: "‘하나님의 이름과 거룩한 성 새 예루살렘의 이름과 예수님의 새 이름을 이기는 자 위에 기록해 주겠다’는 것의 의미는 ①무엇이며, 예수님이 계 2~3장에 약속하신 복은 ②누가 받게 되는가?",
    a: [
      "하나님과 천국과 예수님이 이긴자에게 오심",
      "사단 니골라당과 싸워 이기는 자"
    ]
  },
  {
    q: "계 4:8에서 하나님의 보좌가 장차 이 땅에 임해 오신다고했다. 하나님의 보좌는 계시록 ①몇 장의 어디에 오시며, 그곳의 실체는 ②어디인가?",
    keywords: [
      ["계14장", "12지파", "인", "144,000", "시온산"],
      ["신천지", "예수교", "증거장막성전"]
    ],
    a: [
      "계 14장의 12지파 인 맞은 144,000이 있는 시온산",
      "신천지예수교 증거장막성전"
    ]
  },
  {
    q: "계 5장에서 하나님의 오른손에 있는 ‘일곱 인으로 봉한 책’은 요한계시록이다. 그동안 봉한 책의 내용을 천상천하 아무도 몰랐던 이유는 ①무엇이고, 봉한 책의 내용은 ②무엇인가?",
    a: [
      "실상이 나타나지 않았기 때문",
      "이루어야 할 하나님의 비밀"
    ]
  },
  {
    q: "눅 22:14~20에 새 언약으로 약속하신 ‘예수님의 피와 살’은 ①언제, ②어디에서, ③누가 먹게 되는지 증거 성구 3개와 함께 쓰시오.",
    a: [
      "계시록 성취 때",
      "하나님의 나라 계 7장의 신천지 12지파",
      "144,000과 흰 무리 (계 1:5~6, 계 5:9~10, 계 7:9~14)"
    ]
  },
  {
    q: "계 6장은 ①누가, 누구를 들어, ②누구를 심판한 것이며, 계 6장에서 쫓겨난 자들은 ③몇 장, 몇 장에서 죽임을 당하는가?",
    keywords: [
      ["예수님", "네 생물"],
      ["배도한 선민"],
      ["계8장", "계9장"]
    ],
    a: [
      "예수님, 네 생물",
      "배도한 선민 (일곱 사자와 장막 성도들)",
      "계 8장, 계 9장"
    ]
  },
  {
    q: "마 8장의 바깥 어두운 데로 쫓겨나는 ①‘나라의 본 자손들’과 ②‘동서로부터 와서 천국에 앉는 자들’은 누구인지 각각 관련 성경 장과 함께 쓰시오.",
    keywords: [
      ["부패한 전통 교회", "영적 이스라엘", "계6장", "계13장"],
      ["추수", "인", "신천지 12지파", "계7장", "계14장"]
    ],
    a: [
      "부패한 전통 교회(영적 이스라엘), 계 6장(계 13장)",
      "추수되어 인 맞은 신천지 12지파(영적 새 이스라엘), 계 7장(계 14장)"
    ]
  },
  {
    q: "계 7장의 인 맞은 자들은 ①무슨 일 후, ②어디서 어떻게 온 자들이며, 이들의 실체는 ③누구인가?",
    keywords: [
      ["계6장", "배도한 선천", "해달별", "심판"],
      ["추수 밭", "추수"],
      ["신천지 12지파", "144,000"]
    ],
    a: [
      "계 6장의 배도한 선천 해⋅달⋅별에 대한 심판 후",
      "추수 밭에서 추수되어",
      "신천지 12지파 144,000"
    ]
  },
  {
    q: "계 8장에서 나팔 소리가 나는 이유는 ①무엇인가? 또한 계 8~9장에서 죽임을 당한 자들은 ②누구이며, 1~6번째 나팔 소리와 일곱 번째 나팔 소리는 ③무엇이 다른가?",
    labels: ["①", "②", "③ 1~6번째 나팔", "  일곱 번째 나팔"],
    keywords: [
      ["인", "책", "읽으므로"],
      ["계 6장", "쫓겨난 자들"],
      ["배도자들의 죽음"],
      ["구원의 나팔"]
    ],
    a: [
      "인을 다 뗌으로 책이 열려 읽으므로",
      "계 6장에서 쫓겨난 자들",
      "배도자들의 죽음",
      "구원의 나팔"
    ]
  },
  {
    q: "계시록에서 이방 짐승(멸망자)이 선민 장막을 심판하는 성경 장 3개는 ①무엇이며, 일곱 천사가 받은 일곱 나팔의 실체는 ②무엇인지 성경 장과 함께 쓰시오.",
    keywords: [
      ["계8장", "계9장", "계13장"],
      ["심판", "믿음의 씨", "성도들", "계6장"]
    ],
    a: [
      "계 8장, 계 9장, 계 13장",
      "심판 때 믿음의 씨로 빼낸 성도들, 계 6장"
    ]
  },
  {
    q: "계 9장과 계 19장의 ‘탄 자와 말’은 각각 ①어느 소속의 어떤 존재이며, 계 9장에서 출현한 말의 ②‘머리’와 ③‘꼬리’, 그 말 입에서 나오는 ④‘불, 연기, 유황’은 무엇인가?",
    labels: ["① 계 9장 소속", "  계 19장 소속", "②", "③", "④"],
    a: [
      "마귀 소속 영,육",
      "하나님 소속 영,육",
      "용의 일곱 목자",
      "거짓 선지자",
      "거짓 교리와 교법"
    ]
  },
  {
    q: "계 9:15을 본바, 여섯째 나팔의 재앙에서 범죄한 네 천사에게 ‘죽임을 당하는 사람’은 ①누구이며, 죽임을 당하는 ‘그 연(年), 월, 일, 시’의 실상은 ②언제, 어떤 사건인가?",
    a: [
      "배도한 장막성전 사람들",
      "1981년 9월 20일 14시, 목사 임직식"
    ]
  },
  {
    q: "계 10장에서 요한이 ‘열린 책을 받아먹었다’는 것은 무슨 뜻인가?",
    keywords: ["계시록", "참뜻", "실상", "통달", "인"],
    a: "계시록의 참뜻과 실상을 완전히 깨달아 통달하고 마음에 인 맞아 있는 것"
  },
  {
    q: "초림 때 ‘구약이 성취된 계시’는 ①누가 받아먹고 어디에 증거했는지 증거 성구와 함께 쓰고, 재림 때 ‘신약이 성취된 계시’는 ②누가 받아먹고 어디에 증거하는지 증거 성구와 함께 쓰시오. 또한 계 1:2에 본바, 지시받은 한 사람이 하나님의 말씀을 전했다고 했는데, 그가 전한 이 말씀은 계시록 ③몇 장에서 받은 말씀인지 쓰시오.",
    a: [
      "예수님, 패역한 이스라엘, 겔 3장, 마 15:24",
      "새요한, 죄 가운데 있는 교회들, 계 10장, 계 22:8, 16",
      "계 10장"
    ]
  },
  {
    q: "일곱째 나팔의 실체는 ①누구이며, 일곱째 나팔 소리는 ②무엇인가? 또한 신⋅구약 성경에 ‘일곱째 나팔’이 나오는 성구 4개는 ③무엇인가?",
    a: [
      "약속의 목자 새요한",
      "새요한의 계시 증거",
      "수 6:15~16, 고전 15:51~54, 계 10:7, 계 11:15"
    ]
  },
  {
    q: "‘일곱째 나팔이 불릴 때 있는 일’은 ①무엇인지 쓰고, ‘마지막 나팔의 비밀’은 ②무엇인지 관련 성구 2개와 함께 쓰시오.",
    a: [
      "세상 나라가 하나님의 나라가 됨",
      "부활과 영생 곧 새 피조물로 변화, 계 10:7, 고전 15:51~54"
    ]
  },
  {
    q: "계 12:9~11에 아이와 그 형제들이 용의 무리와 싸워 이긴 무기인 ①‘어린양의 피’와 ②‘증거하는 말’은 무엇이며, 하나님의 나라와 구원은 ③무슨 일 후 있게 되는가? 또한 계 2~3장의 약속은 ④몇 장에서 이루어졌는가?",
    a: [
      "예수님의 생명의 말씀",
      "계시록 성취 실상 증거의 말씀",
      "용의 무리와 싸워 이긴 후",
      "계 12장"
    ]
  },
  {
    q: "계 13장의 사건의 현장인 ‘하늘’은 ①어디이며, 계 13장의 ‘일곱 머리와 열 뿔’은 계시록 ②몇 장의 누구인가?",
    a: [
      "계 2~3장과 동일한 장막성전",
      "계 17장의 바벨론 일곱 목자와 열 명의 권세자"
    ]
  },
  {
    q: "계 14:3의 ‘새 노래’는 ①무엇이며, 온 세상에서 ②누구만이 부르며, 새 노래를 배운 곳의 실상은 ③어디인가?",
    a: [
      "계시록이 성취된 실상의 말씀",
      "12지파 인 맞은 144,000",
      "보좌 앞 시온기독교선교센터"
    ]
  },
  {
    q: "마 13장을 본바, ‘하나님의 씨로 난 자’와 ‘마귀의 씨로 난 자’는 ①무엇으로 알 수 있는가? 또한 각각의 실체와 결과는 ②무엇인가?",
    labels: ["①", "② 하나님 씨로 난 자", "  마귀의 씨로 난 자"],
    a: [
      "추수된 자와 추수되지 못한 자",
      "추수되고 인 맞아 12지파에 등록된 자, 천국과 영생",
      "추수되지도 인 맞지도 못하여 12지파에 등록되지 못한 자, 지옥과 영벌"
    ]
  },
  {
    q: "계 15:2의 ‘이기고 벗어난 자들’은 ①누구인지 관련 성경 장과 함께 쓰고, 이긴 무기는 ②무엇인지 증거 성구 1개와 함께 쓰시오.",
    a: [
      "용의 무리와 싸워 이긴 자들, 계 12장",
      "어린양의 피와 증거하는 말, 계 12:11"
    ]
  },
  {
    q: "계 15:5의 ‘증거장막 성전’에서는 ①누가, ②무엇을 증거하며, 이들만이 증거할 수 있는 이유 2가지는 ③무엇인가?",
    a: [
      "이긴 자들",
      "계시록 전장의 실체",
      "사건의 현장에서 실체들을 보았고, 멸망자와 싸워 이겼기 때문"
    ]
  },
  {
    q: "계시록의 ‘재앙’은 ①몇 가지이며 무엇인지 순서대로 쓰고, ‘계 6장’과 ‘계 16장’의 심판의 ②대상은 어떻게 다른지 쓰시오.",
    labels: ["①", "② 계 6장", "  계 16장"],
    a: [
      "세 가지, 일곱 인, 일곱 나팔, 일곱 대접",
      "배도자들",
      "배도자들과 멸망자들"
    ]
  },
  {
    q: "계 16장의 일곱 금 대접은 계시록의 어디서 나타난 존재인지 ①2개의 장을 쓰고, 대접에서 쏟아지는 것은 ②무엇이며, 대접이 쏟아지는 곳은 ③몇 장의 어디인지 쓰시오.",
    a: [
      "계 12장, 계 15장",
      "하나님의 진노",
      "계 13장 장막과 계 18장 바벨론"
    ]
  },
  {
    q: "계시록에서 ①예수님이 택한 목자가 주는 양식과 ②마귀가 택한 목자가 주는 양식은 무엇인지 각각 관련 성구와 함께 쓰시오.",
    a: [
      "계 2:17 감추었던 만나, 계 10장 계시 말씀, 계 22장 생명나무 과실",
      "계 17:2, 계 18:3 음행의 포도주"
    ]
  },
  {
    q: "‘음행의 포도주’는 ①무엇이며, 계 18장에 본바 무너진 만국과 음행의 포도주를 먹은 자들은 ②누구와 결혼했으며, 결혼하는 신랑과 신부는 ③누구인가?",
    a: [
      "거짓 교리(선악과)",
      "용의 신 사단",
      "사단과 그 소속 교인들"
    ]
  },
  {
    q: "하나님께서 아직 바벨론을 심판하지 않으시는 이유는 ①무엇인지 관련 성구 1개와 함께 쓰고, 이 심판은 ②누가, ③무엇으로 하는 심판인지 쓰시오.",
    a: [
      "계 18:4, 하나님의 백성이 다 나오지 않았기 때문",
      "하나님",
      "불"
    ]
  },
  {
    q: "계 19장은 ①어떤 일 후 있게 되며, 계 19장에서 이루어지는 일 2가지는 ②무엇인가?",
    a: [
      "계 18장의 바벨론 심판 후",
      "영육 결혼, 하나님이 오사 통치하심"
    ]
  },
  {
    q: "계 20장, 용이 잡힌 후 보좌들 위에 앉아 심판하는 권세자들은 ①누구이며, 그리스도로 더불어 왕 노릇 하는 첫째 부활에 참예할 자는 ②누구이며, 천 년 후 심판받는 자들은 ③어떤 자들인가?",
    a: [
      "예수님의 영⋅육 12제자들",
      "순교의 영들과 짐승의 표를 받지 아니하고 하나님의 인 맞은 살아남은 자들",
      "곡과 마곡, 육이 죽은 영들"
    ]
  },
  {
    q: "‘계시록 성취 시작’은 ①몇 장의 무슨 일이며, ‘계시록의 완성’은 ②몇 장의 무슨 일인가?",
    a: [
      "계 1, 2, 3장, 재림 예수님 출현",
      "계 21장의 하나님과 천국이 신천지에 임함"
    ]
  },
  {
    q: "성경 66권 중 가장 중대한 예언은 ①무엇이며, 그중에서도 ②몇 장의 내용이 가장 중대하고, 그 이유는 ③무엇인가?",
    a: [
      "요한계시록",
      "계 21장",
      "하나님의 목적인 새 하늘 새 땅 창조에 대한 약속이기 때문"
    ]
  },
  {
    q: "주 재림 때 계시록이 가장 중요한 ①이유와 증거 성구를 쓰고, ‘최고의 진리’는 ②무엇이며, ③어디에 있는지 쓰시오.",
    a: [
      "가감하면 천국에 못 가기 때문, 계 22:18~19",
      "계 1장부터 계 22장까지",
      "진리의 성읍 신천지"
    ]
  }
];

const bankBible = [
  { 
    q: "다음 성구를 암송하여 쓰시오 (계 1:1~3)",
    labels: ["1절", "2절", "3절"], 
    a: [
      "예수 그리스도의 계시라 이는 하나님이 그에게 주사 반드시 속히 될 일을 그 종들에게 보이시려고 그 천사를 그 종 요한에게 보내어 지시하신 것이라",
      "요한은 하나님의 말씀과 예수 그리스도의 증거 곧 자기의 본 것을 다 증거하였느니라",
      "이 예언의 말씀을 읽는 자와 듣는 자들과 그 가운데 기록한 것을 지키는 자들이 복이 있나니 때가 가까움이라"
    ]
  },
  { 
    q: "다음 성구를 암송하여 쓰시오 (계 7:1~4)",
    labels: ["1절", "2절", "3절", "4절"], 
    a: [
      "이 일 후에 내가 네 천사가 땅 네 모퉁이에 선 것을 보니 땅의 사방의 바람을 붙잡아 바람으로 하여금 땅에나 바다에나 각종 나무에 불지 못하게 하더라",
      "또 보매 다른 천사가 살아계신 하나님의 인을 가지고 해 돋는 데로부터 올라와서 땅과 바다를 해롭게 할 권세를 얻은 네 천사를 향하여 큰 소리로 외쳐",
      "가로되 우리가 우리 하나님의 종들의 이마에 인치기까지 땅이나 바다나 나무나 해하지 말라 하더라",
      "내가 인 맞은 자의 수를 들으니 이스라엘 자손의 각 지파 중에서 인 맞은 자들이 십 사만 사천이니"
    ]
  },
  { 
    q: "다음 성구를 암송하여 쓰시오 (계 10:10~11)",
    labels: ["10절", "11절"], 
    a: [
      "내가 천사의 손에서 작은 책을 갖다 먹어버리니 내 입에는 꿀 같이 다나 먹은 후에 내 배에서는 쓰게 되더라",
      "저가 내게 말하기를 네가 많은 백성과 나라와 방언과 임금에게 다시 예언하여야 하리라 하더라"
    ]
  },
  { 
    q: "다음 성구를 암송하여 쓰시오 (계 20:4~6)",
    labels: ["4절", "5절", "6절"], 
    a: [
      "또 내가 보좌들을 보니 거기 앉은 자들이 있어 심판하는 권세를 받았더라 또 내가 보니 예수의 증거와 하나님의 말씀을 인하여 목 베임을 받은 자의 영혼들과 또 짐승과 그의 우상에게 경배하지도 아니하고 이마와 손에 그의 표를 받지도 아니한 자들이 살아서 그리스도로 더불어 천 년 동안 왕 노릇 하니",
      "(그 나머지 죽은 자들은 그 천 년이 차기까지 살지 못하더라) 이는 첫째 부활이라",
      "이 첫째 부활에 참예하는 자들은 복이 있고 거룩하도다 둘째 사망이 그들을 다스리는 권세가 없고 도리어 그들이 하나님과 그리스도의 제사장이 되어 천 년 동안 그리스도로 더불어 왕 노릇 하리라"
    ]
  },
  { 
    q: "다음 성구를 암송하여 쓰시오 (계 22:18~19)",
    labels: ["18절", "19절"], 
    a: [
      "내가 이 책의 예언의 말씀을 듣는 각인에게 증거하노니 만일 누구든지 이것들 외에 더하면 하나님이 이 책에 기록된 재앙들을 그에게 더하실 터이요",
      "만일 누구든지 이 책의 예언의 말씀에서 제하여 버리면 하나님이 이 책에 기록된 생명나무와 및 거룩한 성에 참예함을 제하여 버리시리라"
    ]
  }
];

const bankJss = [
  {
    q: "예언이 성취되는 실상 때 예언이 이루어진 것을 증거하는 자는 ①누구이며, 오늘날 신약 계시록의 이루어진 실상을 증거하고 있는 인물은 ②누구인가?",
    source: "계시록의 예언과 실상 (43.06.24)",
    keywords: [
      ["실상", "다 본 자"],
      ["신천지", "약속의 목자"]
    ],
    a: [
      "그 이루어진 실상(실체들)을 다 본 자",
      "신천지의 약속의 목자(이긴자, 새요한)"
    ]
  },
  {
    q: "계시록을 가감한 자들과 계시록을 통달한 자들은 각각 성경에서 말하는 ①누구인지 쓰고, 신천지가 계시록을 통달할 수 있는 ②이유는 무엇인가?",
    source: "계시록의 예언과 실상 (43.06.24)",
    a: [
      "가감한 자: 이단 / 통달한 자: 정통",
      "계 6장의 배도한 선민 심판의 일이 이루어진 후 계 7장에서 계시 말씀으로 인 맞아 12지파로 창조된 자들이기 때문"
    ]
  },
  {
    q: "계시록 성취 때인 오늘날 구원받기 위해 신앙인이 갖춰야 할 세 가지 조건은 무엇인가?",
    source: "계시록의 예언과 실상 (43.06.24)",
    a: [
      "실상을 믿고 지켜야 함",
      "계시록을 가감하지 말고 통달해야 함",
      "배도자들, 멸망자들에게 속하지 말고 약속하신 구원자에게 속해야 함"
    ]
  },
  {
    q: "오늘날 계 14장의 144,000이 있는 시온산(신천지)이 천국이라 할 수 있는 성경적 이유 두 가지는 무엇인가?",
    source: "때는 지금 종교 말세를 재창조 때이다 (43.06.10)",
    a: [
      "하나님의 보좌가 와 있기 때문",
      "하나님과 어린양의 보좌로부터 생명수의 강이 흐르고 좌우에 달마다 열두 가지 열매 맺는 생명나무(신천지 12지파)가 있기 때문"
    ]
  },
  {
    q: "마 13장의 두 가지 씨는 ①각각 무엇이며, 이로 인해 나타난 두 가지 말과 두 가지 사람은 ②각각 무엇인가?",
    source: "초림 때와 재림 때 약속의 목자를 왜 믿지 못하였는가 (43.06.02)",
    a: [
      "하나님의 씨: 하나님의 말씀 / 마귀의 씨: 마귀의 말",
      "두 가지 말: 참말(진리)과 거짓말(비진리) / 두 가지 사람: 하나님 소속의 사람과 마귀 소속의 사람"
    ]
  },
  {
    q: "초림 때 유대인들과 주 재림 때 전통 기독교인들이 약속의 목자를 믿지 못하고 핍박한 근본적인 이유는 무엇인가?",
    source: "초림 때와 재림 때 약속의 목자를 왜 믿지 못하였는가 (43.06.02)",
    a: "성경(특히 계시록)에 너무나 무식하였기 때문"
  },
  {
    q: "천국을 소망하는 신앙인이 스스로에게 확인하고 돌아보아야 할 질문 세 가지는 무엇인가?",
    source: "나는 신약 계시록이 말한 누구인가 (43.05.28)",
    a: [
      "나는 참으로 신약 계시록대로 창조되었는가?",
      "나는 계시록을 가감하지 않고 통달하는가?",
      "나는 계시록의 약속을 믿고 지켜 신천지 12지파에 등록되었는가?"
    ]
  },
  {
    q: "성경에 나타난 두 가지 씨, 두 가지 사람, 두 가지 나라는 각각 무엇인지 쓰시오.",
    source: "하나님의 세계와 마귀의 세계 - 천국과 지옥 - (43.05.18)",
    labels: ["두 가지 씨", "두 가지 사람", "두 가지 나라"],
    a: [
      "하나님의 씨, 마귀의 씨",
      "하나님의 씨로 난 사람(하나님 소속), 마귀의 씨로 난 사람(마귀 소속)",
      "하나님의 나라(세계) 곧 천국, 마귀의 나라(세계) 곧 지옥"
    ]
  },
  {
    q: "계시록 성취 때 '진리'의 정의는 무엇인가?",
    source: "하나님의 세계와 마귀의 세계 - 천국과 지옥 - (43.05.18)",
    a: "계시록(예언)의 참뜻과 또 계시록이 이루어진 실상(실체들)과 그것을 증거하는 계시 말씀"
  },
  {
    q: "신천지 교단의 공식 명칭과, 신천지가 온 세계 중 유일하다고 자부하는 자격은 무엇인가?",
    source: "신천지는 온 세계 중 계시록의 실상까지도 통달하는 유일한 교단이며 참 정통이다 (43.05.14)",
    labels: ["교단명", "자격"],
    a: [
      "신천지예수교 증거장막성전",
      "신약 계시록의 참뜻뿐만 아니라 그 실상까지도 통달하는 것"
    ]
  },
  {
    q: "예언이 성취되는 실상 때 일어나는 일 두 가지는 무엇인가?",
    source: "계시록의 예언과 실상 (43.06.24)",
    a: [
      "예언한 것의 실체들이 나타나 자기들에 대하여 예언한 그것들을 이룸",
      "예언이 이루어진 것을 다 본 자가 그 이루어진 실상(실체들)을 증거함"
    ]
  },
  {
    q: "계시록의 말씀을 가감했을 때 받게 되는 결과 두 가지를 증거 성구와 함께 쓰시오.",
    source: "계시록의 예언과 실상 (43.06.24)",
    a: [
      "천국에 못 들어감",
      "저주(재앙)를 받음",
      "증거 성구: 계 22:18-19"
    ]
  },
  {
    q: "계시록에 나오는 세 가지 존재 중 유황 불못 곧 지옥에 들어가는 존재 두 가지를 관련 성구 2개와 함께 쓰시오.",
    source: "계시록의 예언과 실상 (43.06.24)",
    a: [
      "배도자들",
      "멸망자들",
      "관련 성구: 계 19:20, 계 20:10"
    ]
  },
  {
    q: "계시록 성취 때인 오늘날을 '종교 말세'이자 '하나님의 새 나라 재창조 때'라고 하는 성경적 사건 이유 두 가지는 무엇인가?",
    source: "때는 지금 종교 말세를 재창조 때이다 (43.06.10)",
    a: [
      "계 6장에서 하나님의 선민이 배도하여 심판받았고",
      "계 6장의 일 후 계 7장에서부터 인 맞은 12지파가 창조되었기 때문"
    ]
  },
  {
    q: "4복음서에서 예수님이 말씀하신 주 재림 때와 같은 두 시대는 ①어느 때인지 성구와 함께 쓰고, 당시 사람들이 당한 멸망의 방법 두 가지는 ②무엇인가?",
    source: "초림 때와 재림 때 약속의 목자를 왜 믿지 못하였는가 (43.06.02)",
    a: [
      "노아 때, 롯 때 (눅 17:26-30)",
      "홍수, 불과 유황"
    ]
  },
  {
    q: "계 11장에 기록된 주 재림 때 악인들에 의해 일어나는 사건은 무엇인가?",
    source: "초림 때와 재림 때 약속의 목자를 왜 믿지 못하였는가 (43.06.02)",
    a: "또 약속의 목자를 죽이는 것"
  },
  {
    q: "교회를 돈 벌기 위한 사업체로 삼은 거짓 목자(마귀 목자)들이 성도들에게 해 온 거짓말 두 가지는 무엇인가?",
    source: "하나님의 세계와 마귀의 세계 - 천국과 지옥 - (43.05.18)",
    a: [
      "신약 계시록을 가르치지 않거나",
      "계시록을 몰라도 천국 간다고 거짓말을 함"
    ]
  },
  {
    q: "하나님의 씨(진리)로 난 자와 마귀의 씨(비진리)로 난 자가 가는 ①곳과, 진리와 비진리가 각각 의미하는 ②길은 무엇인가?",
    source: "하나님의 세계와 마귀의 세계 - 천국과 지옥 - (43.05.18)",
    labels: ["① 하나님의 씨로 난 자", "  마귀의 씨로 난 자", "② 진리", "  비진리"],
    a: [
      "천국",
      "지옥",
      "하나님의 세계 천국으로 가는 길",
      "마귀의 세계 지옥으로 가는 길"
    ]
  },
  {
    q: "약속의 새 나라 12지파의 천민(天民)이 되기 위해 거쳐야 하는 과정은 무엇인지 증거 성구 장과 함께 쓰시오.",
    source: "신천지는 온 세계 중 계시록의 실상까지도 통달하는 유일한 교단이며 참 정통이다 (43.05.14)",
    a: "신약의 계시 말씀으로 인 맞아야 함 (계 7장)"
  },
  {
    q: "노아 때의 심판 방법과 주 재림 때의 심판 방법은 각각 ①무엇인지 쓰고, 주 재림 때 심판에 대한 참고 성경 장은 ②무엇인가?",
    source: "노아 때, 롯 때와 같은 주 재림 (43.05.11)",
    a: [
      "노아 때: 물로 심판 / 주 재림 때: 불로 심판",
      "계 18장"
    ]
  }
];

let currentExam = []; 
let userAnswers = {}; 
let currentPage = 0;
const totalPages = 5;
const qPerPage = 4;

function shuffle(array) {
  return [...array].sort(() => Math.random() - 0.5);
}

function escapeHtml(str) {
  return String(str || '')
    .replace(/&/g, "&amp;")
    .replace(/</g, "&lt;")
    .replace(/>/g, "&gt;")
    .replace(/"/g, "&quot;")
    .replace(/'/g, "&#039;");
}

function initExam() {
  const selectedNormal = shuffle(bankNormal).slice(0, 12).map(item => ({ ...item, type: 'normal' }));
  const selectedBible = shuffle(bankBible).slice(0, 3).map(item => ({ ...item, type: 'bible' }));
  const selectedJss = shuffle(bankJss).slice(0, 5).map(item => ({ ...item, type: 'jss' }));

  currentExam = shuffle([...selectedNormal, ...selectedBible, ...selectedJss]);
  userAnswers = {};
  currentPage = 0;

  document.getElementById('quiz-container').style.display = 'block';
  document.getElementById('result-area').style.display = 'none';

  renderPage();
}

function renderPage() {
  const area = document.getElementById('questions-area');
  area.innerHTML = '';

  const startIdx = currentPage * qPerPage;
  const endIdx = startIdx + qPerPage;
  const pageQuestions = currentExam.slice(startIdx, endIdx);

  pageQuestions.forEach((q, pageRelIdx) => {
    const globalIdx = startIdx + pageRelIdx;

    let badgeHtml = '';
    if (q.type === 'normal') badgeHtml = '<span class="badge badge-normal">기출</span>';
    else if (q.type === 'bible') badgeHtml = '<span class="badge badge-bible">성구</span>';
    else if (q.type === 'jss') badgeHtml = '<span class="badge badge-jss">진성신</span>';

    const sourceHtml = q.source ? `<div class="source-text">📌 출처: ${escapeHtml(q.source)}</div>` : '';

    let inputHtml = '';

    if (Array.isArray(q.a)) {
      const savedArr = userAnswers[globalIdx] || [];
      inputHtml = q.a.map((subA, subIdx) => {
        const val = escapeHtml(savedArr[subIdx] || '');
        const labelText = (q.labels && q.labels[subIdx]) ? q.labels[subIdx] : circNums[subIdx];
        
        return `
          <div class="sub-input-row">
            <span class="sub-label">${labelText}</span>
            <input type="text" value="${val}" oninput="saveSubAnswer(${globalIdx}, ${subIdx}, this.value)" placeholder="정답 입력..." />
          </div>
        `;
      }).join('');
    } else {
      const val = escapeHtml(userAnswers[globalIdx] || '');
      inputHtml = `<input type="text" style="margin-top:8px;" value="${val}" oninput="saveAnswer(${globalIdx}, this.value)" placeholder="정답을 입력하세요..." />`;
    }

    const qDiv = document.createElement('div');
    qDiv.className = 'question-card';
    qDiv.innerHTML = `
      <div class="question-title">${badgeHtml} Q${globalIdx + 1}. ${escapeHtml(q.q)}</div>
      ${sourceHtml}
      ${inputHtml}
    `;
    area.appendChild(qDiv);
  });

  document.getElementById('progress').innerText = `Page ${currentPage + 1} / ${totalPages}`;
  document.getElementById('btn-prev').style.visibility = currentPage === 0 ? 'hidden' : 'visible';
  
  if (currentPage === totalPages - 1) {
    document.getElementById('btn-next').style.display = 'none';
    document.getElementById('btn-submit').style.display = 'block';
  } else {
    document.getElementById('btn-next').style.display = 'block';
    document.getElementById('btn-submit').style.display = 'none';
  }
}

function saveAnswer(index, value) {
  userAnswers[index] = value;
}

function saveSubAnswer(index, subIndex, value) {
  if (!userAnswers[index] || !Array.isArray(userAnswers[index])) {
    userAnswers[index] = [];
  }
  userAnswers[index][subIndex] = value;
}

function changePage(direction) {
  currentPage += direction;
  renderPage();
  window.scrollTo(0, 0);
}

function checkAnswer(userStr = '', correctStr = '', keywordList = null) {
  if (!userStr || !String(userStr).trim()) return false;

  const clean = (str) => String(str).replace(/[\s\:\/,\.\-\_\(\)（）~]/g, '');
  const pureUser = clean(userStr);
  const pureCorrect = clean(correctStr);

  if (pureUser === pureCorrect) return true;

  if (keywordList && Array.isArray(keywordList) && keywordList.length > 0) {
    return keywordList.every(keyGroup => {
      if (Array.isArray(keyGroup)) {
        return keyGroup.some(synonym => pureUser.includes(clean(synonym)));
      } else {
        return pureUser.includes(clean(keyGroup));
      }
    });
  }

  const words = String(correctStr)
    .replace(/[\(\)（）~]/g, ' ')
    .split(/[\s\:\/,\.\-]+/)
    .map(w => clean(w))
    .filter(w => w.length > 1);

  if (words.length > 0) {
    const matchCount = words.filter(w => pureUser.includes(w)).length;
    if (matchCount / words.length >= 0.5) return true;
  }

  return pureUser.includes(pureCorrect) || pureCorrect.includes(pureUser);
}

function submitExam() {
  if (!confirm("정말 제출하시겠습니까?")) return;

  let score = 0;
  const resultList = document.getElementById('result-list');
  resultList.innerHTML = '';

  currentExam.forEach((q, idx) => {
    let isCorrect = true;
    let feedbackHtml = '';

    if (Array.isArray(q.a)) {
      const uArr = userAnswers[idx] || [];
      let subFeedback = [];

      q.a.forEach((corA, subIdx) => {
        const uVal = uArr[subIdx] || '';
        
        let subKeywords = null;
        if (q.keywords && Array.isArray(q.keywords)) {
          if (Array.isArray(q.keywords[subIdx])) {
            subKeywords = q.keywords[subIdx];
          } else if (typeof q.keywords[subIdx] === 'string') {
            subKeywords = [q.keywords[subIdx]];
          }
        }

        const ok = checkAnswer(uVal, corA, subKeywords);
        if (!ok) isCorrect = false;

        const labelText = (q.labels && q.labels[subIdx]) ? q.labels[subIdx] : circNums[subIdx];

        subFeedback.push(`<div><strong>${labelText} 내 답:</strong> ${escapeHtml(uVal) || '(미입력)'} ${ok ? '⭕' : '❌'}<br><span class="diff-correct">   정답: ${escapeHtml(corA)}</span></div>`);
      });

      feedbackHtml = subFeedback.join('<div style="margin-top:6px;"></div>');
    } else {
      const uAns = userAnswers[idx] || '';
      isCorrect = checkAnswer(uAns, q.a, q.keywords);
      
      feedbackHtml = `
        <div><strong>내 답안:</strong> ${escapeHtml(uAns) || '(미입력)'}</div>
        ${!isCorrect ? `<div style="margin-top:6px;"><strong>정답:</strong> <span class="diff-correct">${escapeHtml(q.a)}</span></div>` : ''}
      `;
    }

    if (isCorrect) score++;

    const card = document.createElement('div');
    card.className = 'question-card';
    
    let resultMark = isCorrect 
      ? '<span class="result-mark mark-correct">⭕</span>' 
      : '<span class="result-mark mark-wrong">❌</span>';

    card.innerHTML = `
      <div class="question-title">Q${idx + 1}. ${escapeHtml(q.q)} ${resultMark}</div>
      <div class="feedback-box">
        ${feedbackHtml}
      </div>
    `;
    resultList.appendChild(card);
  });

  document.getElementById('score-banner').innerText = `총 20문제 중 ${score}문제 맞추셨습니다! (${score * 5}점)`;
  document.getElementById('quiz-container').style.display = 'none';
  document.getElementById('result-area').style.display = 'block';
  window.scrollTo(0, 0);
}

window.onload = initExam;
</script>

</body>
</html>
