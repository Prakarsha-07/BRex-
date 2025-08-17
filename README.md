import java.io.*;
import java.util.*;

public class BrexFeaturesDemo {

    private static final String PREFS_FILE = "user_prefs.properties";

    // Logger helpers
    private static void logInfo(String msg) {
        System.out.println("[INFO] " + msg);
    }

    private static void logWarn(String msg) {
        System.out.println("[WARN] " + msg);
    }

    private static void logErr(String msg) {
        System.err.println("[ERR ] " + msg);
    }

    // Notification preferences class
    static class NotificationPrefs {
        boolean pushEnabled;
        boolean smsEnabled;
        String phoneE164 = "";
        String deviceToken = "";
    }

    // Load preferences from file
    private static void loadNotificationPrefs(NotificationPrefs prefs) {
        Properties props = new Properties();
        try (FileInputStream fis = new FileInputStream(PREFS_FILE)) {
            props.load(fis);
        } catch (IOException ignored) {
            // File may not exist initially
        }

        prefs.pushEnabled = "1".equals(props.getProperty("push_enabled", "0"));
        prefs.smsEnabled = "1".equals(props.getProperty("sms_enabled", "0"));
        prefs.phoneE164 = props.getProperty("phone_e164", "");
        prefs.deviceToken = props.getProperty("device_token", "");
    }

    // Save preferences to file
    private static void saveNotificationPrefs(NotificationPrefs prefs) {
        Properties props = new Properties();
        props.setProperty("push_enabled", prefs.pushEnabled ? "1" : "0");
        props.setProperty("sms_enabled", prefs.smsEnabled ? "1" : "0");
        props.setProperty("phone_e164", prefs.phoneE164);
        props.setProperty("device_token", prefs.deviceToken);

        try (FileOutputStream fos = new FileOutputStream(PREFS_FILE)) {
            props.store(fos, "User Preferences");
        } catch (IOException e) {
            logErr("Cannot write prefs file: " + e.getMessage());
        }
    }

    // Simulated push notification
    private static boolean pushSend(String deviceToken, String title, String body) {
        if (deviceToken == null || deviceToken.isEmpty()) return false;
        logInfo("[PUSH] to " + deviceToken + " | " + title + " - " + body);
        return true;
    }

    // Simulated SMS
    private static boolean smsSend(String phoneE164, String body) {
        if (phoneE164 == null || phoneE164.isEmpty()) return false;
        logInfo("[SMS ] to " + phoneE164 + " | " + body);
        return true;
    }

    // Test notifications
    private static boolean notificationTest(NotificationPrefs prefs) {
        boolean ok = true;
        if (prefs.pushEnabled) ok &= pushSend(prefs.deviceToken, "Test", "Push notifications are working.");
        if (prefs.smsEnabled) ok &= smsSend(prefs.phoneE164, "SMS notifications are working.");
        return ok;
    }

    // Main
    public static void main(String[] args) {
        logInfo("Brex Features Demo starting...");

        NotificationPrefs prefs = new NotificationPrefs();
        loadNotificationPrefs(prefs);

        if (!prefs.pushEnabled && !prefs.smsEnabled) {
            logWarn("Notifications disabled; enabling defaults for demo.");
            prefs.pushEnabled = true;
            prefs.smsEnabled = true;
            prefs.deviceToken = "demo-device-token";
            prefs.phoneE164 = "+15551234567";
            saveNotificationPrefs(prefs);
        }

        if (!notificationTest(prefs)) {
            logWarn("Notification test failed; prompt user to re-auth device/phone.");
        }

        logInfo("Demo complete.");
    }
}
