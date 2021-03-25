
```csharp
public class ReservationService
{
    public ReservationRepository Repository;

    public ReservationService()
    {
        this.Repository = new ReservationRepository();
    }
}
```