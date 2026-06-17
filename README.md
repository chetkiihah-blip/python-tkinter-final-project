# python-tkinter-final-project
import json
import os
import random
from typing import List, Dict, Optional

DATA_FILE = "facts.json"

def load_facts() -> List[Dict]:
    if not os.path.exists(DATA_FILE):
        return []
    try:
        with open(DATA_FILE, "r", encoding="utf-8") as f:
            data = json.load(f)
            if isinstance(data, list):
                return data
            return []
    except (json.JSONDecodeError, IOError):
        return []

def save_facts(facts: List[Dict]) -> bool:
    try:
        with open(DATA_FILE, "w", encoding="utf-8") as f:
            json.dump(facts, f, ensure_ascii=False, indent=2)
        return True
    except IOError:
        return False

def validate_fact(text: str, category: str, max_length: int = 500) -> tuple[bool, str]:
    text = text.strip()
    category = category.strip()

    if not text:
        return False, "Текст факта не может быть пустым."
    if len(text) > max_length:
        return False, f"Текст слишком длинный (максимум {max_length} символов)."
    if not category:
        return False, "Категория не может быть пустой."
    return True, ""

def add_fact(facts: List[Dict], text: str, category: str) -> tuple[bool, str]:
    ok, msg = validate_fact(text, category)
    if not ok:
        return False, msg
    facts.append({"text": text.strip(), "category": category.strip()})
    return True, "Факт добавлен."

def get_random_fact(facts: List[Dict], category: Optional[str] = None) -> Optional[Dict]:
    if not facts:
        return None
    filtered = facts
    if category:
        cat = category.strip().lower()
        filtered = [f for f in facts if f.get("category", "").lower() == cat]
    if not filtered:
        return None
    return random.choice(filtered)

def filter_facts(facts: List[Dict], category: Optional[str] = None) -> List[Dict]:
    if not category:
        return facts[:]
    cat = category.strip().lower()
    return [f for f in facts if f.get("category", "").lower() == cat]
