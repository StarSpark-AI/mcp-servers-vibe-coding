# Design Document Meta-Template (Django Chat Context)

**Purpose:** This meta-template is a script for an AI assistant. Use it to collaborate with the user and generate design document templates tailored to this repository’s Step 4 Django chat app. Default to kebab-case filenames to match repo style: `feature-design-template.md`, `enhancement-design-template.md`, `bug-fix-design-template.md`.

## Repository Context (Read First)
- Project: Simple Django chat app (server-rendered) for Step 4.
- Stack: Django 5.x, Django Templates, SQLite for dev (Postgres later).
- Core model: `Message(session_id, role: user|assistant, content, created_at, user_id?)` with index `(session_id, created_at)`.
- Routes: `GET /` (landing), `GET /chat/`, `POST /chat/send/`.
- Session: cookie-based `session_id`.
- Assistant: deterministic random reply (LLM stub) in Part 1.

Preferred locations when creating files in this step:
- Architecture/Plan/Features under `step4/django-chat/` as instructed in `step4/README.md`.
- Templates under `step4/django-chat/documents/templates/`.

--- 

### AI Assistant Workflow

1.  **Initiate Dialogue:** Ask which template(s) to create: `feature`, `enhancement`, `bug_fix`, or `all`.
2.  **Inquire about Comment Tags:** Ask the user if they have a preferred format for adding comments to the design document (e.g., `<initials>...</initials>`). This will help in distinguishing their comments from the template's content.
3.  **Follow the Development Lifecycle:** For each requested document, follow this lifecycle:
    *   **Requirements:** Gather and clarify the requirements for the feature, enhancement, or bug fix.
    *   **Pattern Analysis:** **CRITICAL:** Analyze existing patterns and docs before designing. Search `step4/README.md`, `AGENTS.md`, and any existing `step4/django-chat/*` docs to stay consistent.
    *   **Analysis & Design:** Propose a design aligned with the Django chat architecture (server-rendered templates, minimal JS, Message model, endpoints). Then create the design doc.
    *   **Design Review & Verification:** **CRITICAL:** Get the design document reviewed and approved. Verify the design with the user before implementation.
    *   **Implementation:** Implement the changes as described in the design document (only after approval).
    *   **Testing & Confirmation:** Write and run tests to verify the changes. The user should then test and confirm the changes before committing the code.
    *   **Documentation:** Once the code is tested and confirmed, update all relevant documentation. Ask the user if any older design documents should be updated as well.
    *   **Commit and Pull Request:** Commit the code and file a pull request. If necessary, update the documentation with a reference to the PR and push a new commit.
4.  **Iterate and Gather Information:** For each template, confirm inclusion of Django-specific examples (models, urls, views, templates, tests).
5.  **Generate Template File(s):** After gathering info, generate the final `.md` file(s) using kebab-case names and the locations above.

--- 

## Part 1: Creating a `feature-design-template.md`

*AI, if the user wants to create a **feature** template, confirm the inclusion of these illustrative examples:*

1.  **Overview & Pattern Analysis:** "Include the feature goal and expected behavior. **CRITICALLY,** add 'Existing Pattern Analysis' (check Step 4 docs for similar patterns: Message model, routes, templates)."
2.  **Database Models:** "Include a concrete Django example. For this app, use `Message` as a sample:
```python
class Message(models.Model):
    session_id = models.CharField(max_length=64, db_index=True)
    role = models.CharField(max_length=16, choices=[('user','user'),('assistant','assistant')])
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    user = models.ForeignKey(settings.AUTH_USER_MODEL, null=True, blank=True, on_delete=models.SET_NULL)

    class Meta:
        indexes = [models.Index(fields=['session_id','created_at'])]
```
Note: In code, use `TextField`; above is illustrative."
3.  **Views, URLs, and API:** "Provide Django-specific examples:
    * URLConf:
```python
urlpatterns = [
    path('', views.landing, name='landing'),
    path('chat/', views.chat_page, name='chat'),
    path('chat/send/', views.chat_send, name='chat-send'),
]
```
    * View skeleton:
```python
def chat_send(request):
    if request.method != 'POST':
        return HttpResponseNotAllowed(['POST'])
    data = json.loads(request.body)
    content = data.get('content','').strip()
    # save user message -> pick stub response -> save assistant message -> return JSON
    return JsonResponse({'messages': messages_payload})
```
    * JSON example for `POST /chat/send/` (request/response)."
4.  **Testing & Tracking:** "Use Django `TestCase` with client requests:
```python
class ChatTests(TestCase):
    def test_send_message_creates_user_and_assistant(self):
        resp = self.client.post('/chat/send/', data=json.dumps({'content':'hi'}), content_type='application/json')
        self.assertEqual(resp.status_code, 200)
```
Include a checklist (migrations, urls, views, templates, tests, docs)."
5.  **AI Instructions:** "Embed the workflow: Requirements → **Pattern Analysis** → Analysis & Design → Verify → Implement → Test & Confirm → Document. Search Step 4 docs and templates. After implementation and confirmation, update docs and reference the PR. Use Claude for authorship, Gemini/Codex for one-pass review."

---

## Part 2: Creating an `enhancement-design-template.md`

*AI, if the user wants to create an **enhancement** template, confirm these details:*

1.  **Core Concept & Pattern Analysis:** "Focus on `Current Behavior` vs `Proposed Behavior`. Add mandatory `Existing Pattern Analysis` (check how current urls/views/templates handle similar flows) and `Impact Analysis` (data migration, UX consistency)."
2.  **Illustrative Examples:** "Use diff-like examples (e.g., add `is_pinned = models.BooleanField(default=False)`), migrations, and updated templates. For server views, prefer `POST` changes over `PUT/PATCH` unless using DRF."
3.  **AI Instructions:** "Same workflow; ensure backward compatibility and migration safety. Update docs and PR references."

---

## Part 3: Creating a `bug-fix-design-template.md`

*AI, if the user wants to create a **bug_fix** template, confirm these structural elements:*

1.  **Structure & Traceability:** "This template needs a repeatable structure for each bug. I will include a `Reference/Ticket` field for linking to a bug tracker like GitHub Issues. Is this the right approach?"
2.  **Pattern Analysis for Consistency:** "Ensure fixes follow Django error handling/validation patterns (e.g., form validation, 4xx/5xx responses, CSRF)."
3.  **Proposed Fix Example:** "The clearest way to show a fix is with a `before-and-after` code snippet. I will include this as the primary example in the `Proposed Fix` section. Agreed?"
4.  **Testing:** "Practice TDD: first write a failing `TestCase` reproducing the bug (e.g., `client.post('/chat/send/', ...)`)."
5.  **AI Instructions:** "I will embed a detailed workflow for the AI assistant. This workflow will follow the 'Requirements -> **Pattern Analysis** -> Analysis & Design -> Verify Design -> Implement -> Test & Confirm -> Document' lifecycle. The Pattern Analysis step ensures bug fixes follow existing error handling patterns. During the 'Analysis & Design' phase, I will instruct the AI to search for existing design documents in the 'features', 'enhancements', and 'bug_fixes' folders. After implementation and confirmation, the AI will update all relevant documentation, including asking if any older documents need updating and adding a reference to the pull request. Is this the right guideline?"

---

## Part 4: General AI Instructions

*AI, for all templates, confirm the inclusion of these general instructions:*

1.  **Dependencies:** "List new dependencies and update `requirements.txt`. For Django changes, include migration commands: `python manage.py makemigrations && python manage.py migrate`."
2.  **Rollback Plan:** "Add a rollback plan (revert migration, feature flag or template toggle, revert PR)."
3.  **Security:** "Call out CSRF, input validation, session handling, and secrets (no keys in repo)."
4.  **Remember User Preferences:** "Remember user formatting and workflow preferences for subsequent docs; update this meta-template if needed."
