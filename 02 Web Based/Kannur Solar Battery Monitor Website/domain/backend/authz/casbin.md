### cenario 1: Restricting access to a specific Device

Since you are using a "Whitelist" approach, **users are restricted by default.** If "Person A" (UserID 5) tries to access "Device B" (ID 10) and you haven't explicitly granted them permission, they are already restricted.

If they **previously** had access and you want to take it away, you simply **Revoke** their permissions from the database.

**How to do it in code:** You can call this function from a service or an Admin controller: