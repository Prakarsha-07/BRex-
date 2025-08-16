# BRex-
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>
#include <time.h>
#define LOG_INFO(fmt, ...) fprintf(stdout, "[INFO] " fmt "\n", ##_VA_ARGS_)
#define LOG_WARN(fmt, ...) fprintf(stdout, "[WARN] " fmt "\n", ##_VA_ARGS_)
#define LOG_ERR(fmt, ...) fprintf(stderr, "[ERR ] " fmt "\n", ##_VA_ARGS_)
static long now_ms(void) {
struct timespec ts; clock_gettime(CLOCK_REALTIME, &ts);
return (long)ts.tv_sec * 1000L + ts.tv_nsec / 1000000L;
}
#define PREFS_FILE "user_prefs.db"
static void kv_set(const char* key, const char* value) {
// Load file into memory (small demo, not for prod scale)
FILE* f = fopen(PREFS_FILE, "r");
char* buf = NULL; size_t len = 0; long sz = 0;
if (f) { fseek(f, 0, SEEK_END); sz = ftell(f); fseek(f, 0, SEEK_SET);
buf = (char*)malloc(sz + 1); if (buf) { fread(buf, 1, sz, f); buf[sz] = '\0'; }
fclose(f);
}
FILE* out = fopen(PREFS_FILE, "w");
if (!out) { LOG_ERR("Cannot open prefs file for write"); return; }
bool replaced = false;
if (buf) {
char* saveptr; char* line = strtok_r(buf, "\n", &saveptr);
while (line) {
char k[256], v[256];
if (sscanf(line, "%255[^=]=%255[^\n]", k, v) == 2) {
if (strcmp(k, key) == 0) {
fprintf(out, "%s=%s\n", key, value);
replaced = true;
} else {
fprintf(out, "%s=%s\n", k, v);
}
}l
ine = strtok_r(NULL, "\n", &saveptr);
} free(buf);
}i
f (!replaced) fprintf(out, "%s=%s\n", key, value);
fclose(out);
}
typedef struct {
bool push_enabled;
bool sms_enabled;
char phone_e164[32];
char device_token[128];
} NotificationPrefs;
static void load_notification_prefs(NotificationPrefs* p) {
char val[128];
if (kv_get("push_enabled", val, sizeof val)) p->push_enabled = (strcmp(val, "1") == 0); else p-
>push_enabled = false;
if (kv_get("sms_enabled", val, sizeof val)) p->sms_enabled = (strcmp(val, "1") == 0); else p-
>sms_enabled = false;
if (kv_get("phone_e164", val, sizeof val)) strncpy(p->phone_e164, val, sizeof p->phone_e164);
else strcpy(p->phone_e164, "");
if (kv_get("device_token", val, sizeof val)) strncpy(p->device_token, val, sizeof p->device_token);
else strcpy(p->device_token, "");
}
static bool kv_get(const char* key, char* out_value, size_t out_len) {
FILE* f = fopen(PREFS_FILE, "r");
if (!f) return false;
char line[512];
while (fgets(line, sizeof(line), f)) {
char k[256], v[256];
if (sscanf(line, "%255[^=]=%255[^\n]", k, v) == 2) {
if (strcmp(k, key) == 0) {
strncpy(out_value, v, out_len - 1);
out_value[out_len - 1] = '\0';
fclose(f); return true;
}
}
} fclose(f); return false;
}
typedef struct {
bool push_enabled;
bool sms_enabled;
char phone_e164[32];
char device_token[128];
} NotificationPrefs;
static void load_notification_prefs(NotificationPrefs* p) {
char val[128];
if (kv_get("push_enabled", val, sizeof val)) p->push_enabled = (strcmp(val, "1") == 0); else p-
>push_enabled = false;
if (kv_get("sms_enabled", val, sizeof val)) p->sms_enabled = (strcmp(val, "1") == 0); else p-
>sms_enabled = false;
if (kv_get("phone_e164", val, sizeof val)) strncpy(p->phone_e164, val, sizeof p->phone_e164);
else strcpy(p->phone_e164, "");
if (kv_get("device_token", val, sizeof val)) strncpy(p->device_token, val, sizeof p->device_token);
else strcpy(p->device_token, "");
}
static void save_notification_prefs(const NotificationPrefs* p) {
kv_set("push_enabled", p->push_enabled ? "1" : "0");
kv_set("sms_enabled", p->sms_enabled ? "1" : "0");
kv_set("phone_e164", p->phone_e164);
kv_set("device_token", p->device_token);
}
static bool push_send(const char* device_token, const char* title, const char* body) {
if (!device_token || !*device_token) return false;
LOG_INFO("[PUSH] to %s | %s - %s", device_token, title, body);
return true; // assume success
}
static bool sms_send(const char* phone_e164, const char* body) {
if (!phone_e164 || !*phone_e164) return false;
LOG_INFO("[SMS ] to %s | %s", phone_e164, body);
return true; // assume success
}
static bool notification_test(NotificationPrefs* p) {
bool ok = true;
if (p->push_enabled) ok &= push_send(p->device_token, "Test", "Push notifications are working.");
if (p->sms_enabled) ok &= sms_send(p->phone_e164, "SMS notifications are working.");
return ok;
}
typedef struct {
long min_interval_ms;
long last_update_ms;
} UIDebouncer;
static void debouncer_init(UIDebouncer* d, long min_interval_ms) {
d->min_interval_ms = min_interval_ms; d->last_update_ms = 0;
}
static bool debouncer_should_update(UIDebouncer* d) {
long t = now_ms();
if (t - d->last_update_ms >= d->min_interval_ms) {
d->last_update_ms = t; return true;
}
return false; // skip this frame to prevent jank/layout shift
}
typedef struct { int view_h; int keyboard_h; int input_h; } UISizes;
static int keyboard_safe_input_y(UISizes s) {
int safe_bottom = s.view_h - s.keyboard_h; // visible area above keyboard
int y = safe_bottom - s.input_h - 8; // 8px padding
if (y < 0) y = 0; return y;
}
// ------------------------- Check Deposit Flow (state machine) -------------------------
typedef enum {
DEP_IDLE = 0,
DEP_CAPTURE_FRONT,
DEP_CAPTURE_BACK,
DEP_SUBMITTING,
DEP_SUCCESS,
DEP_ERROR
} DepositState;
typedef struct {
DepositState state;
char account_id[64];
double amount;
bool front_ok;
bool back_ok;
} DepositSession;
static void deposit_reset(DepositSession* s) {
s->state = DEP_IDLE; s->amount = 0.0; s->front_ok = false; s->back_ok = false; s->account_id[0] =
'\0';
}
static void deposit_start(DepositSession* s, const char* account_id, double amount) {
deposit_reset(s);
strncpy(s->account_id, account_id, sizeof s->account_id);
s->amount = amount;
s->state = DEP_CAPTURE_FRONT;
LOG_INFO("Deposit started: acct=%s amount=%.2f", s->account_id, s->amount);
}
static bool deposit_capture_front(DepositSession* s) {
if (s->state != DEP_CAPTURE_FRONT) return false;
// Camera OCR & validation stub
s->front_ok = true; s->state = DEP_CAPTURE_BACK; LOG_INFO("Front image OK");
return true;
}
static bool deposit_capture_back(DepositSession* s) {
if (s->state != DEP_CAPTURE_BACK) return false;
s->back_ok = true; s->state = DEP_SUBMITTING; LOG_INFO("Back image OK");
return true;
}
static bool api_submit_deposit(const DepositSession* s) {
// Pretend to call backend; success if both images OK and amount > 0
return s->front_ok && s->back_ok && s->amount > 0.0;
}
static bool deposit_submit(DepositSession* s) {
if (s->state != DEP_SUBMITTING) return false;
bool ok = api_submit_deposit(s);
s->state = ok ? DEP_SUCCESS : DEP_ERROR;
LOG_INFO("Deposit submit: %s", ok ? "SUCCESS" : "ERROR");
return ok;
}
static void deposit_another(DepositSession* s, double next_amount) {
// Implements the "Deposit Another" quick action correctly.
char acct[64]; strncpy(acct, s->account_id, sizeof acct);
deposit_start(s, acct, next_amount);
}
typedef enum { ROLE_EMPLOYEE = 0, ROLE_MANAGER = 1, ROLE_CFO = 2 } Role;
typedef struct {
char id[64];
Role role;
char name[64];
} User;
typedef struct {
char id[64];
double amount;
char currency[8];
char merchant[64];
bool approved;
} PaymentRequest;
static bool biometric_check(void) {
// Platform hook: FaceID/TouchID/Android BiometricPrompt
return true; // assume success in demo
}
static bool can_approve(const User* u, const PaymentRequest* p) {
switch (u->role) {
case ROLE_CFO: return true;
case ROLE_MANAGER: return (p->amount <= 5000.0);
case ROLE_EMPLOYEE: return (p->amount <= 200.0);
default: return false;
}
}
static bool approve_payment(User* u, PaymentRequest* p) {
if (!can_approve(u, p)) { LOG_WARN("User %s not permitted to approve %.2f", u->name, p-
>amount); return false; }
if (!biometric_check()) { LOG_WARN("Biometric failed"); return false; }
p->approved = true; LOG_INFO("Payment %s approved by %s (%.2f %s @ %s)", p->id, u->name, p-
>amount, p->currency, p->merchant);
return true;
}
static int tests_run = 0, tests_failed = 0;
#define ASSERT_TRUE(expr, msg) do { tests_run++; if (!(expr)) { tests_failed++; LOG_ERR("ASSERT
FAIL: %s", msg); } } while(0)
static void test_notifications(void) {
NotificationPrefs p = { .push_enabled=true, .sms_enabled=true };
strcpy(p.phone_e164, "+15551234567");
strcpy(p.device_token, "demo-device-token");
save_notification_prefs(&p);
NotificationPrefs q; load_notification_prefs(&q);
ASSERT_TRUE(q.push_enabled && q.sms_enabled, "prefs load/save");
ASSERT_TRUE(notification_test(&q), "notification test sends");
}
static void test_deposit_flow(void) {
DepositSession s; deposit_start(&s, "acct-001", 123.45);
ASSERT_TRUE(s.state == DEP_CAPTURE_FRONT, "state after start");
ASSERT_TRUE(deposit_capture_front(&s), "front capture");
ASSERT_TRUE(deposit_capture_back(&s), "back capture");
ASSERT_TRUE(deposit_submit(&s), "submit ok");
ASSERT_TRUE(s.state == DEP_SUCCESS, "success state");
deposit_another(&s, 50.00);
ASSERT_TRUE(s.state == DEP_CAPTURE_FRONT && s.amount == 50.00, "deposit another resets
flow");
}
static void test_payment_approval(void) {
User mgr = { .role=ROLE_MANAGER }; strcpy(mgr.name, "Casey Manager");
PaymentRequest p; strcpy(p.id, "pay-123"); p.amount = 1200.0; strcpy(p.currency, "USD");
strcpy(p.merchant, "Acme SaaS"); p.approved = false;
ASSERT_TRUE(approve_payment(&mgr, &p), "manager approves 1200");
User emp = { .role=ROLE_EMPLOYEE }; strcpy(emp.name, "Evan Employee");
PaymentRequest p2; strcpy(p2.id, "pay-124"); p2.amount = 350.0; strcpy(p2.currency, "USD");
strcpy(p2.merchant, "Hotel XYZ"); p2.approved = false;
ASSERT_TRUE(!approve_payment(&emp, &p2), "employee blocked >200");
}
static void test_ui_helpers(void) {
UIDebouncer d; debouncer_init(&d, 100); // min 100ms between layout updates
bool a = debouncer_should_update(&d);
bool b = debouncer_should_update(&d); // should be false immediately after
ASSERT_TRUE(a && !b, "debouncer throttles rapid updates");
UISizes s = { .view_h=800, .keyboard_h=300, .input_h=44 };
int y = keyboard_safe_input_y(s);
ASSERT_TRUE(y >= 0 && y <= (800-300-44), "keyboard safe y within visible bounds");
}
static void run_all_tests(void) {
LOG_INFO("Running regression tests...");
test_notifications();
test_deposit_flow();
test_payment_approval();
test_ui_helpers();
LOG_INFO("Tests run: %d | Failed: %d", tests_run, tests_failed);
}
int main(void) {
LOG_INFO("Brex Features Demo starting...");
NotificationPrefs prefs; load_notification_prefs(&prefs);
if (!prefs.push_enabled && !prefs.sms_enabled) {
LOG_WARN("Notifications disabled; enabling defaults for demo.");
prefs.push_enabled = true; prefs.sms_enabled = true;
strcpy(prefs.device_token, "demo-device-token");
strcpy(prefs.phone_e164, "+15551234567");
save_notification_prefs(&prefs);
}
if (!notification_test(&prefs)) {
LOG_WARN("Notification test failed; prompt user to re-auth device/phone.");
}
DepositSession dep; deposit_start(&dep, "acct-main-001", 200.00);
deposit_capture_front(&dep);
deposit_capture_back(&dep);
deposit_submit(&dep);
deposit_another(&dep, 75.25); // quick action works
User cfo = { .role=ROLE_CFO }; strcpy(cfo.name, "Riley CFO");
PaymentRequest pr; strcpy(pr.id, "pay-999"); pr.amount=12500.00; strcpy(pr.currency, "USD");
strcpy(pr.merchant, "Global Vendor");
approve_payment(&cfo, &pr);
run_all_tests();
LOG_INFO("Demo complete.");
return (tests_failed == 0) ? 0 : 1;
}
