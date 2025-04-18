import json
import random
import os
from reportlab.lib.pagesizes import A4
from reportlab.pdfgen import canvas

def load_questions(class_num, subject):
    file_path = f"data/class{class_num}/{subject.lower()}.json"
    if not os.path.exists(file_path):
        print("Question file not found.")
        return []
    with open(file_path, "r") as f:
        data = json.load(f)
        return data.get("questions", [])

def generate_question_paper_pdf(class_num, subject, num_questions=5):
    questions = load_questions(class_num, subject)
    if not questions:
        return
    
    selected = random.sample(questions, min(num_questions, len(questions)))
    filename = f"class{class_num}_{subject.lower()}_question_paper.pdf"
    
    c = canvas.Canvas(filename, pagesize=A4)
    width, height = A4
    y = height - 50

    c.setFont("Helvetica-Bold", 14)
    c.drawString(100, y, f"Question Paper - Class {class_num} - {subject.capitalize()}")
    y -= 40
    c.setFont("Helvetica", 12)

    for i, q in enumerate(selected, 1):
        lines = q.split('\n')
        for line in lines:
            c.drawString(50, y, f"{i}. {line}" if line == lines[0] else f"   {line}")
            y -= 20
        y -= 10

    c.save()
    print(f"PDF generated: {filename}")

if __name__ == "__main__":
    class_num = input("Enter class (5-12): ")
    subject = input("Enter subject (Math, Science, etc.): ")
    generate_question_paper_pdf(class_num, subject)
