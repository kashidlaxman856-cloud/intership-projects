# TIC TAC TOE GAME WITH AI PLAYER
Code Used:import math

# Initialize the board
board = [' ' for _ in range(9)]

def print_board():
    for row in [board[i*3:(i+1)*3] for i in range(3)]:
        print('| ' + ' | '.join(row) + ' |')

def available_moves():
    return [i for i, spot in enumerate(board) if spot == ' ']

def empty_squares():
    return ' ' in board

def num_empty_squares():
    return board.count(' ')

def make_move(square, letter):
    if board[square] == ' ':
        board[square] = letter
        return True
    return False

def check_winner(square, letter):
    # Check row
    row_ind = square // 3
    row = board[row_ind*3 : (row_ind+1)*3]
    if all([spot == letter for spot in row]):
        return True
    # Check column
    col_ind = square % 3
    column = [board[col_ind+i*3] for i in range(3)]
    if all([spot == letter for spot in column]):
        return True
    # Check diagonals
    if square % 2 == 0:
        diagonal1 = [board[i] for i in [0, 4, 8]]
        if all([spot == letter for spot in diagonal1]):
            return True
        diagonal2 = [board[i] for i in [2, 4, 6]]
        if all([spot == letter for spot in diagonal2]):
            return True
    return False

def is_board_full():
    """Check if the board is completely full"""
    return len(available_moves()) == 0

def has_winner(test_board, letter):
    """Check if there's a winner on the given board state"""
    for i in range(9):
        if test_board[i] == letter:
            # Temporarily use global board to check winner
            global board
            old_board = board.copy()
            board = test_board
            result = check_winner(i, letter)
            board = old_board
            if result:
                return True
    return False

# Minimax Algorithm
def minimax(test_board, depth, is_maximizing):
    """
    Minimax algorithm for Tic-Tac-Toe AI
    test_board: current board state
    depth: current depth in the game tree
    is_maximizing: True if AI turn, False if human turn
    """
    max_player = 'O'  # AI
    min_player = 'X'  # Human
    
    # Check terminal states
    if has_winner(test_board, max_player):
        return {'score': 10 - depth, 'move': None}
    elif has_winner(test_board, min_player):
        return {'score': depth - 10, 'move': None}
    elif is_board_full():
        return {'score': 0, 'move': None}
    
    if is_maximizing:
        # AI's turn - maximize score
        best_score = -math.inf
        best_move = None
        
        for move in available_moves():
            test_board[move] = max_player
            result = minimax(test_board, depth + 1, False)
            test_board[move] = ' '
            
            if result['score'] > best_score:
                best_score = result['score']
                best_move = move
        
        return {'score': best_score, 'move': best_move}
    else:
        # Human's turn - minimize score
        best_score = math.inf
        best_move = None
        
        for move in available_moves():
            test_board[move] = min_player
            result = minimax(test_board, depth + 1, True)
            test_board[move] = ' '
            
            if result['score'] < best_score:
                best_score = result['score']
                best_move = move
        
        return {'score': best_score, 'move': best_move}

def ai_move():
    """Get the best move for the AI using minimax"""
    result = minimax(board.copy(), 0, True)
    return result['move']

# Game Loop
def play():
    global board
    board = [' ' for _ in range(9)]
    
    print("Welcome to AI Tic-Tac-Toe! You are 'X' and go first.")
    print("The AI is 'O' and will try to beat you!")
    print_board()
    print("\nPositions are numbered 0-8:")
    print(" 0 | 1 | 2")
    print("-----------")
    print(" 3 | 4 | 5")
    print("-----------")
    print(" 6 | 7 | 8\n")
    
    letter = 'X'
    
    while empty_squares():
        if letter == 'O':
            print("\nAI is thinking...")
            move = ai_move()
            
            if move is not None:
                make_move(move, 'O')
                print(f"AI makes a move to square {move}")
                print_board()
                
                if check_winner(move, 'O'):
                    print("\nAI wins! Better luck next time.")
                    return
                
                letter = 'X'
            else:
                print("Draw!")
                return
        else:
            try:
                move = int(input("\nYour turn (0-8): "))
                
                if move < 0 or move > 8:
                    print("Please enter a number between 0 and 8.")
                    continue
                
                if move not in available_moves():
                    print("That square is already taken. Try again.")
                    continue
                
                make_move(move, 'X')
                print_board()
                
                if check_winner(move, 'X'):
                    print("\nWow! You won! (This should be nearly impossible!)")
                    return
                
                letter = 'O'
            except ValueError:
                print("Please enter a valid number between 0 and 8.")
    
    print("\nIt's a draw!")

if __name__ == '__main__':




# AI CHATBOT FOR COLLEGE HELPDESK
CODE: import re
import random

class CollegeChatbot:
    def __init__(self):
        # Define intents, keywords, and matching responses
        self.rules = {
            'greeting': {
                'keywords': [r'\bhello\b', r'\bhi\b', r'\bhey\b', r'\bsup\b', r'\bgreetings\b'],
                'responses': ["Hello! Welcome to the College Helpdesk. How can I assist you today?", 
                              "Hi there! Ask me anything about admissions, exams, or campus facilities.",
                              "Welcome! What information can I help you with?"]
            },
            'admissions': {
                'keywords': [r'\badmission\b', r'\badmissions\b', r'\bapply\b', r'\bregistration\b', r'\bfees\b', r'\benrollment\b'],
                'responses': [
                    "Admissions for the upcoming semester are open! You can apply online via the college portal. The application fee is $50, and the deadline is next month.",
                    "To apply for admission, visit our official website and fill out the online form. You'll need your academic transcripts and entrance exam scores.",
                    "The admission process is simple: Submit your application, pay the fee, and wait for the merit list. Need any specific details?"
                ]
            },
            'exams': {
                'keywords': [r'\bexam\b', r'\bexams\b', r'\btest\b', r'\bschedule\b', r'\btimetable\b', r'\bmidterm\b', r'\bfinal\b'],
                'responses': [
                    "The mid-term exam schedule has been posted on the student notice board. Final exams are scheduled to begin from the first week of next month. Make sure your dues are cleared!",
                    "Exam schedules are available on the student portal. Remember to carry your hall ticket and valid ID during exams.",
                    "For exam-related queries, you can also visit the Academic Office. They're open from 9 AM to 5 PM on weekdays."
                ]
            },
            'facilities': {
                'keywords': [r'\blibrary\b', r'\bcampus\b', r'\bhostel\b', r'\bgym\b', r'\bwi-fi\b', r'\bwifi\b', r'\bfacilities\b', r'\blab\b'],
                'responses': [
                    "Our campus features a 24/7 library, fully equipped computer labs, high-speed Wi-Fi, separate hostels for boys and girls, and a modern sports complex.",
                    "Campus facilities include: Central Library (24/7 access), Computer Labs, Cafeteria, Gymnasium, Sports Court, and High-Speed Internet throughout campus.",
                    "All students have access to our world-class facilities. Hostel accommodation is available for both male and female students."
                ]
            },
            'contact': {
                'keywords': [r'\bcontact\b', r'\bphone\b', r'\bemail\b', r'\baddress\b', r'\boffice\b'],
                'responses': [
                    "You can reach us at:\n📧 Email: administration@college.edu\n📞 Phone: +1-800-COLLEGE\n📍 Address: 123 Education Lane, College City",
                    "Contact Information:\n- Main Office: +1-800-COLLEGE\n- Email: info@college.edu\n- Admissions: admissions@college.edu"
                ]
            },
            'goodbye': {
                'keywords': [r'\bbye\b', r'\bgoodbye\b', r'\bexit\b', r'\bthank you\b', r'\bthanks\b', r'\bquit\b'],
                'responses': [
                    "You're welcome! Feel free to reach out if you have more questions. Have a great day!",
                    "Goodbye! Glad I could help. Good luck with your studies!",
                    "Thank you for using College Helpdesk. See you soon!"
                ]
            }
        }
        self.fallback_responses = [
            "I'm sorry, I didn't quite catch that. Could you please rephrase your question regarding admissions, exams, or campus facilities?",
            "I am still learning! For specific queries like that, please contact the main office at administration@college.edu.",
            "That's an interesting question! Could you provide more details? I can help with admissions, exams, facilities, or contact information."
        ]
        self.conversation_history = []

    def classify_intent(self, user_message):
        """Classify user intent based on keywords"""
        user_message = user_message.lower().strip()
        
        # Handle empty input
        if not user_message:
            return 'unknown'
        
        # Loop through intents to find a keyword match (Intent Classification)
        for intent, data in self.rules.items():
            for pattern in data['keywords']:
                if re.search(pattern, user_message):
                    return intent
        return 'unknown'

    def get_response(self, intent):
        """Get a random response for the classified intent"""
        if intent in self.rules:
            return random.choice(self.rules[intent]['responses'])
        return random.choice(self.fallback_responses)

    def save_conversation(self, user_msg, bot_response):
        """Save conversation for future reference"""
        self.conversation_history.append({
            'user': user_msg,
            'bot': bot_response
        })

    def display_help(self):
        """Display available commands and topics"""
        print("\n" + "="*50)
        print("📚 AVAILABLE TOPICS:")
        print("="*50)
        print("✓ Admissions & Registration")
        print("✓ Exams & Schedules")
        print("✓ Campus Facilities")
        print("✓ Contact Information")
        print("✓ Type 'help' for this menu")
        print("✓ Type 'history' to see conversation log")
        print("✓ Type 'exit' or 'bye' to quit")
        print("="*50 + "\n")

    def start_chat(self):
        """Start the chatbot conversation"""
        print("\n" + "="*60)
        print("🎓 COLLEGE HELPDESK AI CHATBOT ACTIVE 🎓")
        print("="*60)
        print("Welcome! I'm here to help with college information.")
        print("Type 'help' for available topics or 'exit' to quit.")
        print("="*60)
        
        while True:
            try:
                user_input = input("\n📝 You: ").strip()
                
                # Handle empty input
                if not user_input:
                    print("Bot: Please enter a question or type 'help' for available topics.")
                    continue
                
                # Handle special commands
                if user_input.lower() == 'help':
                    self.display_help()
                    continue
                
                if user_input.lower() == 'history':
                    if self.conversation_history:
                        print("\n📜 CONVERSATION HISTORY:")
                        for i, msg in enumerate(self.conversation_history, 1):
                            print(f"{i}. You: {msg['user']}")
                            print(f"   Bot: {msg['bot']}\n")
                    else:
                        print("Bot: No conversation history yet.")
                    continue
                
                # Classify intent and get response
                intent = self.classify_intent(user_input)
                response = self.get_response(intent)
                
                print(f"🤖 Bot: {response}")
                
                # Save to conversation history
                self.save_conversation(user_input, response)
                
                # Check if user wants to exit
                if intent == 'goodbye':
                    print("\n" + "="*60)
                    print("Thank you for using College Helpdesk!")
                    print("="*60)
                    break
                    
            except KeyboardInterrupt:
                print("\n\nBot: Chat ended. Goodbye!")
                break
            except Exception as e:
                print(f"Bot: An error occurred: {str(e)}. Please try again.")

if __name__ == "__main__":
    chatbot = CollegeChatbot()
    chatbot.start_chat()



# AI BASED RESUME SHORTLISTING SYSTEM 
CODE:# You will need scikit-learn installed for this code to run. 
# Command: pip install scikit-learn

from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import json
import csv
from datetime import datetime

class ResumeShortlistingSystem:
    def __init__(self, job_description):
        self.job_description = job_description
        self.vectorizer = TfidfVectorizer(stop_words='english', min_df=1, max_df=0.9)
        self.rankings = []
        self.threshold = 0.0

    def rank_candidates(self, candidates_data, threshold=0.0):
        """
        Takes a dictionary of candidate names and their resume text, 
        calculates similarity against the JD, and returns a ranked list.
        
        Args:
            candidates_data: Dictionary with candidate names and resume text
            threshold: Minimum match percentage to include candidate (0-100)
        """
        self.threshold = threshold
        ranked_results = []
        
        # Combine JD with all resumes for efficient vectorization
        all_documents = [self.job_description] + list(candidates_data.values())
        
        # Fit TF-IDF vectorizer once with all documents
        tfidf_matrix = self.vectorizer.fit_transform(all_documents)
        
        # Calculate similarity for each candidate against JD
        for idx, (name, resume_text) in enumerate(candidates_data.items(), start=1):
            # Cosine similarity between JD (index 0) and Resume (index idx)
            similarity_matrix = cosine_similarity(tfidf_matrix[0:1], tfidf_matrix[idx:idx+1])
            
            # Extract score percentage
            match_percentage = round(float(similarity_matrix[0][0]) * 100, 2)
            
            ranked_results.append({
                "rank": None,
                "name": name,
                "score": match_percentage,
                "status": "Shortlisted" if match_percentage >= threshold else "Not Shortlisted"
            })
        
        # Sort candidates based on highest score
        ranked_results.sort(key=lambda x: x['score'], reverse=True)
        
        # Assign final ranks (only for shortlisted candidates)
        rank_counter = 1
        for result in ranked_results:
            if result['status'] == "Shortlisted":
                result['rank'] = rank_counter
                rank_counter += 1
            else:
                result['rank'] = '-'
        
        self.rankings = ranked_results
        return ranked_results

    def display_rankings(self, show_all=True):
        """Display rankings in a formatted table"""
        if not self.rankings:
            print("No rankings available. Please run rank_candidates() first.")
            return
        
        print("\n" + "="*80)
        print("📋 AI-BASED RESUME RANKING RESULTS 📋")
        print("="*80)
        print(f"Threshold Score: {self.threshold}%")
        print("="*80)
        print(f"{'Rank':<6} {'Name':<25} {'Match Score':<15} {'Status':<20}")
        print("-"*80)
        
        for candidate in self.rankings:
            if show_all or candidate['status'] == "Shortlisted":
                print(f"{str(candidate['rank']):<6} {candidate['name']:<25} {candidate['score']}%{'':<10} {candidate['status']:<20}")
        
        print("="*80)
        
        # Summary statistics
        shortlisted_count = sum(1 for c in self.rankings if c['status'] == "Shortlisted")
        print(f"\n📊 Summary: {shortlisted_count} candidates shortlisted out of {len(self.rankings)}")

    def get_shortlisted_candidates(self):
        """Return only shortlisted candidates"""
        return [c for c in self.rankings if c['status'] == "Shortlisted"]

    def export_to_csv(self, filename="resume_rankings.csv"):
        """Export rankings to CSV file"""
        if not self.rankings:
            print("No rankings to export. Run rank_candidates() first.")
            return
        
        try:
            with open(filename, 'w', newline='') as csvfile:
                fieldnames = ['Rank', 'Candidate Name', 'Match Score (%)', 'Status']
                writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
                
                writer.writeheader()
                for candidate in self.rankings:
                    writer.writerow({
                        'Rank': candidate['rank'],
                        'Candidate Name': candidate['name'],
                        'Match Score (%)': candidate['score'],
                        'Status': candidate['status']
                    })
            
            print(f"✅ Results exported to {filename}")
        except Exception as e:
            print(f"❌ Error exporting to CSV: {str(e)}")

    def export_to_json(self, filename="resume_rankings.json"):
        """Export rankings to JSON file"""
        if not self.rankings:
            print("No rankings to export. Run rank_candidates() first.")
            return
        
        try:
            export_data = {
                "export_date": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
                "threshold": self.threshold,
                "total_candidates": len(self.rankings),
                "shortlisted_count": sum(1 for c in self.rankings if c['status'] == "Shortlisted"),
                "rankings": self.rankings
            }
            
            with open(filename, 'w') as jsonfile:
                json.dump(export_data, jsonfile, indent=4)
            
            print(f"✅ Results exported to {filename}")
        except Exception as e:
            print(f"❌ Error exporting to JSON: {str(e)}")

    def get_top_candidates(self, n=5):
        """Get top N candidates"""
        return self.rankings[:n]

    def get_candidate_score(self, candidate_name):
        """Get score for a specific candidate"""
        for candidate in self.rankings:
            if candidate['name'].lower() == candidate_name.lower():
                return candidate
        return None

    def filter_by_score(self, min_score, max_score=100):
        """Filter candidates by score range"""
        filtered = [c for c in self.rankings if min_score <= c['score'] <= max_score]
        return filtered


if __name__ == "__main__":
    # Sample Job Description
    sample_jd = """
    We are looking for a Software Engineer with strong proficiency in Python. 
    The ideal candidate should have experience with Data Structures, Machine Learning, 
    and Natural Language Processing (NLP). Experience with SQL databases and Git is required.
    Proficiency in algorithms and problem-solving is essential.
    """
    
    # Mock data simulating parsed text extracted from candidate resumes
    sample_resumes = {
        "Laxman": "Python developer with 5 years experience in Machine Learning, NLP, and Data Structures. Expert with SQL and Git. Strong algorithms knowledge.",
        "Shivji": "Frontend Web Developer specialized in React, JavaScript, HTML, and CSS. Beginner in Python programming.",
        "Sudarshan": "Data Scientist with advanced skills in Python, SQL, and Machine Learning models. Strong understanding of algorithms and data structures.",
        "Rushikesh": "Full Stack Developer with Java background. Learning Python. Some experience with databases but limited ML/NLP knowledge.",
        "Adesh": "Python enthusiast with projects in machine learning and data analysis. Good understanding of algorithms. Experience with Git and MySQL."
    }
    
    print("====================================================")
    print("🚀 AI-BASED RESUME SHORTLISTING SYSTEM 🚀")
    print("====================================================\n")
    
    # Initialize system with the job description
    shortliner = ResumeShortlistingSystem(sample_jd)
    
    # Process and get rankings with 30% threshold
    print("⏳ Processing resumes...\n")
    rankings = shortliner.rank_candidates(sample_resumes, threshold=30.0)
    
    # Display all rankings
    shortliner.display_rankings(show_all=True)
    
    # Display top 3 candidates
    print("\n🏆 TOP 3 CANDIDATES:")
    print("="*80)
    top_candidates = shortliner.get_top_candidates(3)
    for idx, candidate in enumerate(top_candidates, 1):
        print(f"{idx}. {candidate['name']} - {candidate['score']}% match")
    
    # Display only shortlisted candidates
    print("\n✅ SHORTLISTED CANDIDATES (Above 30%):")
    print("="*80)
    shortlisted = shortliner.get_shortlisted_candidates()
    for candidate in shortlisted:
        print(f"• {candidate['name']} - {candidate['score']}%")
    
    # Filter by score range
    print("\n🔍 CANDIDATES WITH 50-80% MATCH:")
    print("="*80)
    filtered = shortliner.filter_by_score(50, 80)
    for candidate in filtered:
        print(f"• {candidate['name']} - {candidate['score']}%")
    
    # Export results
    print("\n📤 EXPORTING RESULTS:")
    print("="*80)
    shortliner.export_to_csv("resume_rankings.csv")
    shortliner.export_to_json("resume_rankings.json")
    
    print("\n" + "="*80)
    print("✨ Processing complete! Files saved successfully.")
    print("="*80)
