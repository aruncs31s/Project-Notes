---
tags:
  - project
  - backend
  - kannur_solar_battery_monitor_website
---

# Device

## Search

Currently there is 3 api for search , which are separated by types i can just make it to one api

- [ ] Normalize search api 🔼 📅 ⏳ 2026-05-01

The device search is beeing used in searching micro controllers when adding connected device so, implement this new method

- [ ] Implement the following method.
```go
SearchDevices(
	ctx context.Context,
	userID uint,
	hardwareTypes []string,
	query string,
) ([]dto.GenericDropdown, error)
```
