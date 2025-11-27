# telegram-to-do-bot
A project to demonstrate skills
# Telegram ToDo Bot

A fully-featured Telegram bot for task management with reminders, built with Python and aiogram 3.x.

## Features

### Task Management
- Add tasks with descriptions
- Edit existing tasks
- Delete tasks
- Mark tasks as complete or incomplete
- View all tasks with their current status

### Smart Reminders
- Set reminders with flexible time formats
- Support for relative time: 10m (minutes), 2h (hours)
- Support for absolute time: tomorrow (next day at 9:00 AM)
- Automatic notifications delivered at specified time
- Reliable scheduling powered by APScheduler

### User Interface
- Inline keyboards for easy navigation
- Interactive buttons for all actions
- Clean and intuitive design
- FSM-based dialog management for smooth conversations

### Data Persistence
- All tasks stored in JSON format
- Data survives bot restarts
- Per-user task isolation
- Automatic data saving

## Technologies

- Python 3.8 or higher
- aiogram 3.15.0 - modern async Telegram Bot API framework
- APScheduler 3.10.4 - background task scheduling
- asyncio - asynchronous I/O operations
- JSON - lightweight data storage

## Installation

### Prerequisites
You will need:
- Python 3.8 or higher installed
- A Telegram account
- A bot token from @BotFather

### Setup Instructions

1. Clone the repository
```bash
git clone https://github.com/yourusername/telegram-todo-bot.git
cd telegram-todo-bot
```

2. Install required dependencies
```bash
pip install -r requirements.txt
```

3. Obtain your bot token from @BotFather
- Open Telegram and search for @BotFather
- Send the /newbot command
- Follow the instructions to create your bot
- Save the token you receive

4. Configure the bot token

You can provide the token in two ways:

Using environment variable:
```bash
export BOT_TOKEN='your_token_here'
```

Using a .env file:
```bash
echo "BOT_TOKEN=your_token_here" > .env
```

5. Run the bot
```bash
python bot.py
```

6. Start using the bot
- Find your bot in Telegram by username
- Send /start command
- Begin adding tasks

## Available Commands

- /start - Initialize the bot and display welcome message
- /help - Show help information and usage instructions
- /list - Display all your tasks
- /add - Add a new task

## Usage Guide

### Adding a Task

1. Send the /add command to the bot
2. The bot will ask "What task would you like to add?"
3. Type your task description
4. Choose whether to add a reminder or skip this step
5. Task is created and saved

### Setting Reminders

After adding a task, you can set a reminder using these formats:
- 10m - reminder in 10 minutes
- 2h - reminder in 2 hours
- tomorrow - reminder next day at 9:00 AM

### Managing Tasks

1. Send the /list command
2. Click on any task to view its details
3. Use the provided buttons to:
   - Edit the task description
   - Toggle completion status
   - Delete the task
   - Return to task list

## Architecture

### Project Structure

```
telegram-todo-bot/
├── bot.py                 Main bot implementation (477 lines)
├── requirements.txt       Python dependencies
├── .env.example          Environment variables template
├── tasks.example.json    Example data structure
├── README.md             Documentation
├── QUICKSTART.md         Quick setup guide
├── ARCHITECTURE.md       Technical architecture details
├── DEMO.md              Interface demonstration
├── TESTING.md           Test scenarios and procedures
├── SKILLS.md            Technologies and skills showcase
└── INDEX.md             Project navigation
```

### Core Components

**TaskStorage Class**

Handles all data operations:
- load_data() - Load tasks from JSON file
- save_data() - Persist tasks to JSON file
- add_task() - Create a new task
- delete_task() - Remove a task
- toggle_task() - Change task completion status
- edit_task() - Modify task description
- get_task_by_id() - Retrieve specific task

**TaskStates FSM**

Manages conversation states:
- waiting_for_task - Awaiting task text input
- waiting_for_reminder - Awaiting reminder time input
- editing_task - In edit mode

**Command Handlers**

Process user commands:
- /start - Welcome message handler
- /help - Help information handler
- /list - Task list display handler
- /add - New task creation handler

**Callback Handlers**

Process button interactions:
- task_{id} - Display task details
- add_task - Initiate task creation
- delete_{id} - Remove specific task
- toggle_{id} - Toggle task completion
- edit_{id} - Start task editing
- back_to_list - Return to task list

## Code Examples

### Basic Usage

```python
from bot import TaskStorage

# Initialize storage
storage = TaskStorage()

# Add a task
task_id = storage.add_task(
    user_id=123456,
    text="Buy groceries",
    reminder="2h"
)

# Retrieve user tasks
tasks = storage.get_user_tasks(123456)

# Mark task as complete
storage.toggle_task(123456, task_id)
```

### Task Processing

```python
# Get all tasks for a user
tasks = storage.data[user_id]

# Filter completed tasks
completed = [task for task in tasks if task['completed']]

# Calculate statistics
total_tasks = len(tasks)
completed_count = len([t for t in tasks if t['completed']])
pending_count = total_tasks - completed_count
```

## Configuration

### Environment Variables

BOT_TOKEN - Your Telegram bot token (required)

### Data Format

Tasks are stored in JSON with the following structure:

```json
{
  "user_id": [
    {
      "id": 1,
      "text": "Task description",
      "completed": false,
      "created_at": "2024-11-27T10:30:00",
      "reminder": "2h"
    }
  ]
}
```

## Testing

Refer to TESTING.md for comprehensive testing information including:
- 20 detailed test scenarios
- Pre-launch verification checklist
- Known limitations and constraints
- Step-by-step testing procedures

### Quick Test Procedure

1. Basic Functionality Tests
   - Add a task using /add command
   - View task list with /list
   - Complete a task using toggle button
   - Delete a task

2. Reminder Tests
   - Create task with 1m reminder
   - Wait one minute
   - Verify notification received

3. Edge Case Tests
   - Submit empty task text
   - Use invalid reminder format
   - Create multiple tasks
   - Restart bot and verify data persistence

## Project Statistics

- Total code: 477 lines of Python
- Documentation: over 1,500 lines
- Total files: 13
- Classes: 2 main classes
- Commands: 4 bot commands
- Features: 8 core features
- Project size: approximately 55 KB

## Deployment

### Local Development

```bash
python bot.py
```

### Production Deployment with systemd

1. Create service file
```bash
sudo nano /etc/systemd/system/todobot.service
```

2. Add the following configuration
```ini
[Unit]
Description=Telegram ToDo Bot
After=network.target

[Service]
Type=simple
User=youruser
WorkingDirectory=/path/to/bot
Environment="BOT_TOKEN=your_token"
ExecStart=/usr/bin/python3 bot.py
Restart=always

[Install]
WantedBy=multi-user.target
```

3. Enable and start the service
```bash
sudo systemctl start todobot
sudo systemctl enable todobot
```

### Docker Deployment

Create a Dockerfile:

```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["python", "bot.py"]
```

Build and run:
```bash
docker build -t todobot .
docker run -e BOT_TOKEN=your_token todobot
```

## Security Considerations

- Bot token is stored in environment variables, not in code
- User data is isolated on a per-user basis
- No sensitive information is stored
- JSON file has appropriate file system permissions

## Troubleshooting

### Bot fails to start
- Verify BOT_TOKEN is correctly set
- Check Python version is 3.8 or higher
- Ensure all dependencies are installed

### Reminders not functioning
- Confirm APScheduler is properly installed
- Verify bot runs continuously without interruption
- Check system clock is correctly set

### Tasks not persisting
- Verify write permissions for tasks.json
- Check JSON file integrity
- Ensure sufficient disk space

## Future Development

Potential enhancements for future versions:
- Task categories and tagging system
- Recurring tasks (daily, weekly, monthly)
- Task export functionality
- Statistics and analytics dashboard
- Search and filter capabilities
- Multi-language interface support
- Database storage migration (SQLite or PostgreSQL)
- Task sharing between users

## License

This project is licensed under the MIT License. See the LICENSE file for details.

## Author

Your Name
GitHub: github.com/your_username
Email: nazarijsevcuk69@gmail.com

## Acknowledgments

This project uses the following open-source libraries:
- aiogram - Telegram Bot framework for Python
- APScheduler - Advanced Python Scheduler
- Telegram Bot API - Official Telegram Bot platform

## Support

For bug reports and feature requests:
- Create an issue on GitHub
- Contact via email
- Reach out on Telegram

## Related Projects

You may also be interested in:
- Excel Automation - Automated Excel report generation
- News Parser - Web scraping and news aggregation
- Text Analyzer - Text analysis with data visualization
- Currency API - RESTful currency conversion service

---

Last updated: November 2024
Version: 1.0
Status: Production Ready
