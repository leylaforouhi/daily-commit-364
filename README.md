
import json
from pathlib import Path
from datetime import date, timedelta

FILE = Path("maintenance.json")

def load_data():
    if FILE.exists():
        return json.loads(FILE.read_text(encoding="utf-8"))
    return []

def save_data(data):
    FILE.write_text(
        json.dumps(data, indent=4, ensure_ascii=False),
        encoding="utf-8"
    )

def add_record(data):
    vehicle = input("Vehicle name: ").strip()
    service = input("Service description: ").strip()

    try:
        cost = float(input("Maintenance cost: "))
        interval = int(input("Next service in how many days? "))

        if not vehicle or not service or cost < 0 or interval < 0:
            print("Invalid information.")
            return

        next_service = date.today() + timedelta(days=interval)

        data.append({
            "vehicle": vehicle,
            "service": service,
            "cost": cost,
            "date": str(date.today()),
            "next_service": str(next_service)
        })

        save_data(data)
        print("Maintenance record saved!")

    except ValueError:
        print("Please enter valid numbers.")

def show_records(data):
    if not data:
        print("No maintenance records.")
        return

    for index, item in enumerate(data, 1):
        print(f"\n{index}. {item['vehicle']}")
        print(f"Service: {item['service']}")
        print(f"Cost: {item['cost']:.2f}")
        print(f"Next service: {item['next_service']}")

        if item["next_service"] <= str(date.today()):
            print("Status: Service due!")

def main():
    data = load_data()

    while True:
        print("\n1. Add maintenance")
        print("2. Show records")
        print("3. Show total costs")
        print("4. Exit")

        choice = input("Choose: ")

        if choice == "1":
            add_record(data)

        elif choice == "2":
            show_records(data)

        elif choice == "3":
            total = sum(item["cost"] for item in data)
            print(f"Total maintenance costs: {total:.2f}")

        elif choice == "4":
            break

        else:
            print("Invalid choice.")

if __name__ == "__main__":
    main()
