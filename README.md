<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Exam Results - QUEST COACHING CENTRE</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  
  <!-- Firebase SDK -->
  <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore-compat.js"></script>
  
  <style>
    * { margin:0; padding:0; box-sizing:border-box; font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
    body { background:#f5f5f5; min-height:100vh; display:flex; justify-content:center; align-items:center; padding:20px; }
    .container { width:100%; max-width:1200px; background:rgba(255,255,255,0.95); border-radius:15px; box-shadow:0 15px 30px rgba(0,0,0,0.2); overflow:hidden; }

    /* Header */
    .header { background:linear-gradient(to right,#1a237e,#283593,#303f9f); color:white; padding:25px; text-align:center; }
    .school-name { font-size:2.5rem; font-weight:700; }
    .location { font-size:1.2rem; margin-bottom:15px; }
    .director-info { display:flex; align-items:center; justify-content:center; margin-top:20px; flex-wrap:wrap; }
    .director-photo { width:120px; height:120px; border-radius:50%; border:5px solid white; overflow:hidden; margin-right:20px; }
    .director-photo img{width:100%;height:100%;object-fit:cover;}
    .director-details{text-align:left;}
    .contact-line{background:#ffc107;color:#333;padding:12px;text-align:center;font-weight:600;font-size:1.1rem;}

    /* Toppers */
    .toppers-section{padding:30px;background:#f5f5f5;text-align:center;}
    .promo-text{font-size:1.1rem;line-height:1.8;color:#1a237e;font-weight:600;margin-bottom:30px;white-space:pre-line;text-align:left;}
    .toppers-title{font-size:1.6rem;color:#b21f1f;margin:20px 0;text-align:center;}
    .all-toppers{display:flex;justify-content:center;flex-wrap:wrap;gap:15px;}
    .topper-card{background:white;border-radius:10px;box-shadow:0 8px 15px rgba(0,0,0,0.1);overflow:hidden;text-align:center;transition:transform .3s;width:170px;padding:10px;}
    .topper-card:hover{transform:translateY(-5px);}
    .topper-rank{background:#1a237e;color:white;padding:6px;font-weight:600;font-size:0.9rem;}
    .topper-photo{width:80px;height:80px;border-radius:50%;margin:15px auto;border:3px solid #1a237e;overflow:hidden;}
    .topper-photo img{width:100%;height:100%;object-fit:cover;}
    .topper-name{font-size:0.95rem;color:#333;margin-top:5px;height:40px;display:flex;align-items:center;justify-content:center;}
    .topper-percentage{font-size:1.1rem;color:#1a237e;font-weight:700;margin:8px 0;}

    /* Firebase Results Section */
    .results-section { padding:30px; background:#f9f9f9; }
    .section-title { font-size:1.8rem; color:#1a237e; text-align:center; margin-bottom:25px; padding-bottom:10px; border-bottom:2px solid #1a237e; }
    
    .results-list { display:grid; grid-template-columns:repeat(auto-fill, minmax(300px, 1fr)); gap:20px; margin-bottom:30px; }
    .result-item { background:white; padding:20px; border-radius:10px; box-shadow:0 5px 15px rgba(0,0,0,0.1); cursor:pointer; transition:all 0.3s; border-left:4px solid #1a237e; }
    .result-item:hover { transform:translateY(-5px); box-shadow:0 8px 20px rgba(0,0,0,0.15); }
    .result-item.active { border-left-color:#ffc107; background:#fffae6; }
    .exam-name { font-size:1.2rem; color:#1a237e; font-weight:600; margin-bottom:10px; }
    .exam-date { font-size:0.9rem; color:#666; margin-bottom:5px; }
    .exam-students { font-size:0.9rem; color:#666; }
    
    .roll-input-section { background:white; padding:25px; border-radius:10px; box-shadow:0 5px 15px rgba(0,0,0,0.1); margin-top:20px; text-align:center; display:none; }
    .roll-input-section h3 { color:#1a237e; margin-bottom:20px; }
    .input-group { display:flex; justify-content:center; gap:10px; flex-wrap:wrap; }
    .input-group input { padding:12px 15px; font-size:1rem; border-radius:5px; border:1px solid #ccc; width:250px; }
    .result-btn { padding:12px 25px; background:#1a237e; color:white; font-size:1rem; border:none; border-radius:8px; cursor:pointer; transition:background 0.3s; }
    .result-btn:hover { background:#283593; }
    
    /* Result Card */
    .result-card { display:none; max-width:700px; margin:30px auto; background:white; border-radius:15px; box-shadow:0 10px 30px rgba(0,0,0,0.2); padding:30px; }
    .result-card-header { text-align:center; margin-bottom:25px; padding-bottom:15px; border-bottom:2px solid #1a237e; }
    .result-card-header h2 { color:#1a237e; margin-bottom:5px; }
    .result-card-header h3 { color:#b21f1f; margin-bottom:5px; }
    .result-card-header p { color:#666; font-size:0.9rem; }
    
    .student-info { margin-bottom:25px; }
    .info-row { display:flex; justify-content:space-between; margin-bottom:10px; padding:10px 15px; background:#f5f5f5; border-radius:5px; }
    .info-label { font-weight:600; color:#1a237e; }
    .info-value { color:#333; }
    
    .marks-table { width:100%; border-collapse:collapse; margin-bottom:25px; }
    .marks-table th { background:#1a237e; color:white; padding:12px; text-align:left; }
    .marks-table td { padding:12px; border-bottom:1px solid #ddd; }
    .marks-table tr:nth-child(even) { background:#f9f9f9; }
    .marks-table tr:hover { background:#f0f0f0; }
    
    .total-marks { background:#e8f5e8 !important; font-weight:600; color:#1a237e; }
    .percentage-display { text-align:center; font-size:1.5rem; color:#1a237e; font-weight:700; padding:15px; background:#f0f7ff; border-radius:10px; margin-top:20px; }
    .coaching-mention { text-align:center; margin-top:20px; padding-top:15px; border-top:1px solid #ddd; color:#666; font-style:italic; }
    
    .loading { text-align:center; padding:20px; font-size:1.1rem; color:#1a237e; }
    .error { text-align:center; padding:20px; background:#ffe6e6; color:#b30000; border-radius:5px; margin:20px 0; }
    
    /* Footer */
    .footer { background:#1a237e; color:white; text-align:center; padding:20px; font-size:1rem; }

  </style>
</head>
<body>
  <div class="container">
    <!-- Header -->
    <div class="header">
      <h1 class="school-name">QUEST COACHING CENTRE</h1>
      <p class="location">In Front of S.S. Girls High School, Gopalganj</p>
      <div class="director-info">
        <div class="director-photo"><img src="https://i.ibb.co/hF9tmnpB/20250903-202922.jpg"></div>
        <div class="director-details">
          <h2>CEO & FOUNDER</h2>
          <h3>MR. RAVI SIR</h3>
          <p><i class="fas fa-phone"></i> Call: +91 9708442525</p>
        </div>
      </div>
    </div>

    <!-- Contact Line -->
    <div class="contact-line">If you want your result online contact: 8580594404</div>

    <!-- Firebase Results Section -->
    <div class="results-section">
      <h2 class="section-title">📊 Check Your Results</h2>
      
      <div class="loading" id="loading-results">
        <i class="fas fa-spinner fa-spin"></i> Loading available results...
      </div>
      
      <div class="results-list" id="results-list" style="display:none;"></div>
      
      <div class="roll-input-section" id="roll-input-section">
        <h3 id="selected-exam-name">Enter your Roll Number</h3>
        <div class="input-group">
          <input type="number" id="roll-input" placeholder="Enter Roll Number">
          <button class="result-btn" onclick="fetchStudentResult()">📑 Get Result</button>
          <button class="result-btn" onclick="goBackToResults()" style="background:#666;">⬅ Back</button>
        </div>
      </div>
      
      <div class="result-card" id="result-card">
        <div class="result-card-header">
          <h2>QUEST COACHING CENTRE</h2>
          <h3 id="result-exam-name">Exam Name</h3>
          <p>Under the guidance of <strong>MR. RAVI SIR</strong></p>
        </div>
        
        <div class="student-info">
          <div class="info-row">
            <span class="info-label">Student Name:</span>
            <span class="info-value" id="result-student-name">-</span>
          </div>
          <div class="info-row">
            <span class="info-label">Father's Name:</span>
            <span class="info-value" id="result-father-name">-</span>
          </div>
          <div class="info-row">
            <span class="info-label">Roll Number:</span>
            <span class="info-value" id="result-roll-no">-</span>
          </div>
        </div>
        
        <table class="marks-table">
          <thead>
            <tr>
              <th>Subject</th>
              <th>Marks Obtained</th>
              <th>Total Marks</th>
              <th>Percentage</th>
            </tr>
          </thead>
          <tbody id="marks-table-body">
            <!-- Marks will be populated here -->
          </tbody>
        </table>
        
        <div class="percentage-display" id="total-percentage">
          Total Percentage: 0%
        </div>
        
        <div class="coaching-mention">
          <p>Result provided by QUEST COACHING CENTRE, Gopalganj | Director: MR. RAVI SIR</p>
          <p>For any queries, contact: +91 9708442525</p>
        </div>
      </div>
    </div>

    <!-- Toppers Section - ALL 30 TOPPERS -->
    <div class="toppers-section">
      <div class="promo-text">
मात्र 3,000/- में संपूर्ण विषयों कि तैयारी करे एवं बिहार बोर्ड परीक्षा 2026 में 480+ अंक लाए।  

1. सभी विषयों का प्रिंटेट नोट्स  
2. चेप्टर वाइज VVI Objective प्रश्न  
3. चेप्टर वाइज VVI Subjective प्रश्न  
4. चेप्टर वाइज टेस्ट सीरीज  
5. पिछले 20 वर्षो का Question Bank का हल  
6. बोर्ड परीक्षा में 480+ अंक लाने का टिप्स & ट्रिक्स  
7. 400+ अंक नही आने पर पूरा पैसा (Fee) वापस with Agreement Sign ..!  

✨ प्रत्येक साल की तरह इस साल भी 1 अक्टूबर 2025 से "सफलता एक्सप्रेस कोर्स" शुरू होने जा रहा है।  
🗓 जिसके लिए 01 सितम्बर 2025 से सीट बूकिंग शुरू होगा।  
🎯 जिसका एक ही मकसद होगा कि किसी भी हाल में 2026 में होने वाले बिहार बोर्ड परीक्षा में छात्रों को टॉप-10 में शामिल किया जाए।  
      </div>

      <h2 class="toppers-title">सफलता एक्सप्रेस के कुछ सफल छात्र</h2>
      <div class="all-toppers">
        <!-- ALL 30 TOPPERS -->
        <div class="topper-card">
          <div class="topper-rank">1st</div>
          <div class="topper-photo"><img src="https://i.ibb.co/Jw8CzGzQ/IMG-20250904-WA0012.jpg"></div>
          <h3 class="topper-name">रिंकी कुमारी</h3>
          <div class="topper-percentage">469 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">2nd</div>
          <div class="topper-photo"><img src="https://i.ibb.co/XxMqpmH5/IMG-20250904-WA0013.jpg"></div>
          <h3 class="topper-name">साहिल कुमार</h3>
          <div class="topper-percentage">465 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">3rd</div>
          <div class="topper-photo"><img src="https://i.ibb.co/G3VVPSqZ/IMG-20250904-WA0014.jpg"></div>
          <h3 class="topper-name">रौनक कुमार</h3>
          <div class="topper-percentage">453 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">4th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/TxNS2sQ8/IMG-20250904-WA0015.jpg"></div>
          <h3 class="topper-name">सत्यम कुमार सिंह</h3>
          <div class="topper-percentage">447 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">5th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/991nS5Cx/IMG-20250904-WA0016.jpg"></div>
          <h3 class="topper-name">अभिराज कुमार</h3>
          <div class="topper-percentage">446 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">6th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/pj5mp7JF/IMG-20250904-WA0017.jpg"></div>
          <h3 class="topper-name">सोनी कुमारी</h3>
          <div class="topper-percentage">446 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">7th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/JwGMNwF6/IMG-20250904-WA0018.jpg"></div>
          <h3 class="topper-name">रिशु कुमारी राय</h3>
          <div class="topper-percentage">445 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">8th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/JWhkzfbG/IMG-20250904-WA0019.jpg"></div>
          <h3 class="topper-name">तपेश्वरी कुमारी</h3>
          <div class="topper-percentage">444 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">9th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/21DCkH7y/IMG-20250904-WA0020.jpg"></div>
          <h3 class="topper-name">राजकृत कुमार</h3>
          <div class="topper-percentage">444 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">10th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/4nn6YZdt/IMG-20250904-WA0021-1.jpg"></div>
          <h3 class="topper-name">तरुण कुमार</h3>
          <div class="topper-percentage">439 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">11th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/yFDsffpF/IMG-20250904-WA0022.jpg"></div>
          <h3 class="topper-name">रमिता कुमारी</h3>
          <div class="topper-percentage">439 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">12th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/v6MCpZ4R/IMG-20250904-WA0023.jpg"></div>
          <h3 class="topper-name">जिज्ञासा कुमारी</h3>
          <div class="topper-percentage">434 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">13th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/kggFk370/IMG-20250904-WA0024.jpg"></div>
          <h3 class="topper-name">खुश्बू कुमारी</h3>
          <div class="topper-percentage">431 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">14th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/7J0J30c7/IMG-20250904-WA0025.jpg"></div>
          <h3 class="topper-name">रिया कुमारी</h3>
          <div class="topper-percentage">430 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">15th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/7JRgb628/IMG-20250904-WA0027.jpg"></div>
          <h3 class="topper-name">करण कुमार</h3>
          <div class="topper-percentage">427 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">16th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/bjSNtXPz/IMG-20250904-WA0028.jpg"></div>
          <h3 class="topper-name">रिशु कुमारी</h3>
          <div class="topper-percentage">425 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">17th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/ZpkDP0pF/IMG-20250904-WA0029.jpg"></div>
          <h3 class="topper-name">ईशु कुमारी</h3>
          <div class="topper-percentage">425 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">18th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/4Zcy07cj/IMG-20250904-WA0030.jpg"></div>
          <h3 class="topper-name">लक्की कुमार</h3>
          <div class="topper-percentage">422 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">19th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/Y4b3gBHK/IMG-20250904-WA0031.jpg"></div>
          <h3 class="topper-name">अशोक कुमार</h3>
          <div class="topper-percentage">422 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">20th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/nN8pWkvv/IMG-20250904-WA0032.jpg"></div>
          <h3 class="topper-name">हर्षित कुमार</h3>
          <div class="topper-percentage">422 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">21st</div>
          <div class="topper-photo"><img src="https://i.ibb.co/hhNYK2y/IMG-20250904-WA0033.jpg"></div>
          <h3 class="topper-name">प्रियंका कुमारी</h3>
          <div class="topper-percentage">418 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">22nd</div>
          <div class="topper-photo"><img src="https://i.ibb.co/tp1HQ0DN/IMG-20250904-WA0034.jpg"></div>
          <h3 class="topper-name">संस्कृति कुमारी</h3>
          <div class="topper-percentage">417 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">23rd</div>
          <div class="topper-photo"><img src="https://i.ibb.co/5XSrj1rh/IMG-20250904-WA0035-1.jpg"></div>
          <h3 class="topper-name">नव्या कुमारी</h3>
          <div class="topper-percentage">414 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">24th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/gMGvJ7Xs/IMG-20250905-WA0007.jpg"></div>
          <h3 class="topper-name">अंकुश कुमार</h3>
          <div class="topper-percentage">409 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">25th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/hRD1Jmcm/IMG-20250905-WA0008.jpg"></div>
          <h3 class="topper-name">संजना कुमारी</h3>
          <div class="topper-percentage">404 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">26th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/BHJjbmjY/IMG-20250905-WA0009.jpg"></div>
          <h3 class="topper-name">रोशनी गुप्ता</h3>
          <div class="topper-percentage">403 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">27th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/60r3W27h/IMG-20250905-WA0010.jpg"></div>
          <h3 class="topper-name">आदित्य कुमार</h3>
          <div class="topper-percentage">402 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">28th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/WW1KTJQz/IMG-20250905-WA0011.jpg"></div>
          <h3 class="topper-name">रोशनी कुमारी</h3>
          <div class="topper-percentage">402 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">29th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/gbgfm4S2/IMG-20250905-WA0012-1.jpg"></div>
          <h3 class="topper-name">रुपाली कुमारी</h3>
          <div class="topper-percentage">402 अंक</div>
        </div>
        <div class="topper-card">
          <div class="topper-rank">30th</div>
          <div class="topper-photo"><img src="https://i.ibb.co/nSG3RX7/IMG-20250905-WA0013.jpg"></div>
          <h3 class="topper-name">स्नेहा कुमारी</h3>
          <div class="topper-percentage">401 अंक</div>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <div class="footer">
      <p>&copy; 2023 QUEST COACHING CENTRE. All Rights Reserved.</p>
    </div>
  </div>

  <!-- Firebase Configuration and JavaScript -->
  <script>
    // Firebase Configuration
    const firebaseConfig = {
      apiKey: "AIzaSyBoAqrgPwwM7GNyURqXrJEYWPihKKR8VGg",
      authDomain: "quest-coaching.firebaseapp.com",
      databaseURL: "https://quest-coaching-default-rtdb.asia-southeast1.firebasedatabase.app",
      projectId: "quest-coaching",
      storageBucket: "quest-coaching.firebasestorage.app",
      messagingSenderId: "131948734089",
      appId: "1:131948734089:web:f4ba25110c8b1298360858"
    };

    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    const db = firebase.firestore();

    // Global variables
    let allResults = [];
    let selectedExamId = null;
    let selectedExamData = null;

    // Fetch all results from Firestore
    async function fetchAllResults() {
      try {
        const querySnapshot = await db.collection("results").get();
        allResults = [];
        
        querySnapshot.forEach((doc) => {
          const data = doc.data();
          allResults.push({
            id: doc.id,
            examName: data.examName || "Unnamed Exam",
            createdAt: data.createdAt ? new Date(data.createdAt.toDate()).toLocaleDateString('en-IN', {
              day: 'numeric',
              month: 'long',
              year: 'numeric'
            }) : "Date not available",
            studentCount: data.students ? data.students.length : 0,
            data: data
          });
        });
        
        // Sort by date (newest first)
        allResults.sort((a, b) => {
          return b.data.createdAt?.toDate() - a.data.createdAt?.toDate();
        });
        
        displayResultsList();
      } catch (error) {
        console.error("Error fetching results:", error);
        document.getElementById("loading-results").innerHTML = 
          '<div class="error">Error loading results. Please try again later.</div>';
      }
    }

    // Display results list
    function displayResultsList() {
      const resultsList = document.getElementById("results-list");
      const loading = document.getElementById("loading-results");
      
      if (allResults.length === 0) {
        loading.innerHTML = '<div class="error">No results available at the moment.</div>';
        return;
      }
      
      loading.style.display = "none";
      resultsList.style.display = "grid";
      
      let html = "";
      allResults.forEach((result, index) => {
        html += `
          <div class="result-item" onclick="selectExam('${result.id}', ${index})" id="result-item-${index}">
            <div class="exam-name">${result.examName}</div>
            <div class="exam-date">📅 Published: ${result.createdAt}</div>
            <div class="exam-students">👨‍🎓 Students: ${result.studentCount}</div>
          </div>
        `;
      });
      
      resultsList.innerHTML = html;
    }

    // Select an exam
    function selectExam(examId, index) {
      selectedExamId = examId;
      selectedExamData = allResults[index].data;
      
      // Highlight selected item
      document.querySelectorAll('.result-item').forEach(item => {
        item.classList.remove('active');
      });
      document.getElementById(`result-item-${index}`).classList.add('active');
      
      // Show roll number input section
      document.getElementById('selected-exam-name').textContent = 
        `Enter your Roll Number for: ${selectedExamData.examName}`;
      document.getElementById('results-list').style.display = 'none';
      document.getElementById('roll-input-section').style.display = 'block';
      document.getElementById('result-card').style.display = 'none';
      
      // Clear previous input
      document.getElementById('roll-input').value = '';
    }

    // Go back to results list
    function goBackToResults() {
      document.getElementById('results-list').style.display = 'grid';
      document.getElementById('roll-input-section').style.display = 'none';
      document.getElementById('result-card').style.display = 'none';
      selectedExamId = null;
      selectedExamData = null;
      
      // Remove active class
      document.querySelectorAll('.result-item').forEach(item => {
        item.classList.remove('active');
      });
    }

    // Fetch student result
    function fetchStudentResult() {
      const rollInput = document.getElementById('roll-input').value.trim();
      
      if (!rollInput) {
        alert("Please enter your roll number");
        return;
      }
      
      if (!selectedExamData || !selectedExamData.students) {
        alert("Exam data not loaded properly. Please go back and select again.");
        return;
      }
      
      // Find student by roll number
      const student = selectedExamData.students.find(s => s.rollNo == rollInput);
      
      if (!student) {
        alert("Roll number not found in this exam.");
        return;
      }
      
      // Display result
      displayStudentResult(student);
    }

    // Display student result
    function displayStudentResult(student) {
      // Hide input section, show result card
      document.getElementById('roll-input-section').style.display = 'none';
      const resultCard = document.getElementById('result-card');
      resultCard.style.display = 'block';
      
      // Set exam info
      document.getElementById('result-exam-name').textContent = selectedExamData.examName;
      
      // Set student info
      document.getElementById('result-student-name').textContent = student.name || "-";
      document.getElementById('result-father-name').textContent = student.fatherName || "-";
      document.getElementById('result-roll-no').textContent = student.rollNo || "-";
      
      // Calculate and display marks
      const marksTable = document.getElementById('marks-table-body');
      let html = '';
      let totalObtained = 0;
      let totalPossible = 0;
      
      if (student.marks && selectedExamData.subjects) {
        // Loop through subjects from exam data
        selectedExamData.subjects.forEach(subject => {
          const subjectName = subject.name;
          const subjectTotal = subject.total || 100;
          const marksObtained = student.marks[subjectName] || 0;
          const percentage = ((marksObtained / subjectTotal) * 100).toFixed(2);
          
          html += `
            <tr>
              <td>${subjectName}</td>
              <td>${marksObtained}</td>
              <td>${subjectTotal}</td>
              <td>${percentage}%</td>
            </tr>
          `;
          
          totalObtained += marksObtained;
          totalPossible += subjectTotal;
        });
        
        // Add total row
        const totalPercentage = totalPossible > 0 ? ((totalObtained / totalPossible) * 100).toFixed(2) : 0;
        html += `
          <tr class="total-marks">
            <td><strong>Total</strong></td>
            <td><strong>${totalObtained}</strong></td>
            <td><strong>${totalPossible}</strong></td>
            <td><strong>${totalPercentage}%</strong></td>
          </tr>
        `;
        
        document.getElementById('total-percentage').textContent = 
          `Total Percentage: ${totalPercentage}%`;
      } else {
        html = '<tr><td colspan="4" style="text-align:center;">Marks data not available</td></tr>';
      }
      
      marksTable.innerHTML = html;
      
      // Scroll to result
      resultCard.scrollIntoView({ behavior: 'smooth' });
    }

    // Initialize on page load
    document.addEventListener('DOMContentLoaded', fetchAllResults);
  </script>
</body>
</html>
