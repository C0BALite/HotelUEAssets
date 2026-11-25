# MHS-22 · Подключение ПК ресепшена

Экран стойки использует экземпляры очередей и менеджера комнат с текущей карты. На `L_MHS13_ReceptionQueues` и `DemoMotelNewLight` Check-In определяется по `IsCheckIn = true`. Вторая очередь обслуживает выезд.

| На экране | Откуда читать или куда отправлять |
| --- | --- |
| Приём гостей | `BP_MHS13_ReceptionQueue.IntakeAllowed` на Check-In. Действие игрока вызывает `SetIntakeAllowed(Allowed)` на этой очереди. Закрытие приёма останавливает появление новых гостей. |
| Размер очередей | `GetQueueCount` включает место обслуживания, `GetWaitingCount` считает ожидающих позади него. Для обновления подпишись на `OnQueueChanged` обеих очередей. |
| Гость у стойки | `GetFirstGuest`, затем `GetGuestWaitSeconds(Guest)`. В `BP_MHS13_Guest` доступны `CheckInRequest`, `CheckOutRequest`, `RoomNumber` и `Satisfaction`. |
| Номер | `BP_RoomManager.FindRoom(Index)` принимает номер комнаты. В `BP_Room` доступны `RoomState`, `Occupant`, `NeedsCleaning` и `RoomKeyActor`. |
| Баланс и рейтинг | `BP_RoomManager.HotelBalanceCents` и `HotelRating`. Для суммы в долларах дели центы на 100. |
| Итог выезда | `BP_RoomManager.OnCheckoutSettled(Result)` отправляет подтверждённую операцию. `Result` содержит `StayId`, `RoomNumber`, `DaysStayed`, `AmountCents` и фактический `RatingDelta`. Последняя операция доступна в `LastCheckoutResult`. |

Для экрана итогов смены используй `OnCheckoutSettled`, который приходит после зачисления. `OnServiceResolved` относится к очереди и вызывается раньше, пока стойка ещё завершает передачу ключа. Сам экран только читает результат и баланс.

На демонстрационной карте у `BP_RoomManager` включён `UseTestCheckoutTimer`. При подключении календаря отключи его и вызывай `StayHotel.RequestCheckout(Guest, StayId, DaysStayed)` в конце проживания. Сохраняй `StayId`, по нему номера и ключи можно повторно использовать в разных проживаниях.

Гостя для текущего показа задаёт `GuestClass` на обеих очередях. `BP_MHS13_Guest` создаёт модель через `GuestAppearance` и `AppearanceClass`; в текущем ассете уже настроены отдельные анимации тела и лица. Для замены модели проверь совместимость её скелета и назначения анимаций тела и лица.
