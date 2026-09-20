---
title: "Использование"
draft: false
type: "translate"
---
### Сервис

Очень легко. Нужно:

1. Создать класс, который наследуется от Service
2. Добавить несколько полей, аналогично добавлению их в формы Django
3. Определить метод `process()`, содержащий бизнес-логику
4. Дополнительно можно использовать метод `post_process()` для выполнения дополнительных задач. Если используется db_transaction = True, это будет запускаться после процесса с использованием транзакции Django при фиксации, которая будет запущена только в случае успешной фиксации транзакции. Если используется db_transaction = False, это будет выполняться после процесса до тех пор, пока нет необработанных исключений. `post_process` полезен для запуска задачи, которая должна выполниться только в случае успешного выполнения процесса (например, отправка электронного письма, вызов задачи celery и т.д.). 

Образец кода стоит тысячи слов.

#### your_app/services.py
```py
class CreateBookingService(Service):
    name = forms.CharField()
    email = forms.EmailField()
    checkin_date = forms.DateField()
    checkout_date = forms.DateField()

    def process(self):
        name = self.cleaned_data['name']
        email = self.cleaned_data['email']
        checkin_date = self.cleaned_data['checkin_date']
        checkout_date = self.cleaned_data['checkout_date']

        # Update or create a customer
        customer = Customer.objects.update_or_create(
            email=email,
            defaults={
                'name': name
            }
        )

        # Create booking
        self.booking = Booking.objects.create(
            customer=customer,
            checkin_date=checkin_date,
            checkout_date=checkout_date,
            status=Booking.PENDING_VERIFICATION,
        )

        return self.booking

    def post_process(self):
        # Send verification email (check out django-herald)
        VerifyEmailNotification(self.booking).send()
```

### Транзакции базы данных

По умолчанию метод процесса для сервисов выполняется внутри транзакции. Это сделано для того, чтобы, если во время выполнения вашей службы возникает исключение, база данных возвращается в исходное состояние. Если вам не нужно такое поведение, вы можете установить db_transaction = False для сервисного класса.

#### your_app/services.py
```py
class NoDbTransactionService(Service):
    db_transaction = False
```

### Представление, основанное на функции

#### your_app/views.py
```py
from django.shortcuts import redirect, render

from .forms import BookingForm
from .services import CreateBookingService


def create_booking_view(request):
    form = BookingForm()

    if request.method == 'POST':
        form = BookingForm(request.POST)
        if form.is_valid():
            try:
              # Services raise InvalidInputsError if you pass
              # invalid values into it.
              CreateBookingService.execute({
                  'name': form.cleaned_data['name'],
                  'email': form.cleaned_data['email'],
                  'checkin_date': form.cleaned_data['checkin_date'],
                  'checkout_date': form.cleaned_data['checkout_date'],
              })
              return redirect('booking:success')
            except Exception as e:
                form.add_error(None, f'Sorry. Something went wrong: {e}')

    return render(request, 'booking/create_booking.html', {'form': form})
```

### Представление, основанное на классе

#### your_app/views.py
```py
from django.core.urlresolvers import reverse_lazy

from service_objects.views import ServiceView

from .forms import BookingForm
from .services import CreateBookingService


class CreateBookingView(ServiceView):
    form_class = BookingForm
    service_class = CreateBookingService
    template_name = 'booking/create_booking.html'
    success_url = reverse_lazy('booking:success')
```

### Тестирование

Пример тестирования `CreateBookingService`

#### your_app/tests.py
```py
from datetime import date

from django.core import mail
from django.test import TestCase

from .models import Booking, Customer
from .services import CreateBookingService


class CreateBookingServiceTest(TestCase):

    def test_create_booking(self):
        inputs = {
            'name': 'John Doe',
            'email': 'john@doe.com',
            'checkin_date': date(2017, 8, 13),
            'checkout_date': date(2017, 8, 15),
        }

        booking = CreateBookingService.execute(inputs)

        # Test that a Customer gets created
        customer = Customer.objects.get()
        self.assertEqual(customer.name, inputs['name'])
        self.assertEqual(customer.email, inputs['email'])

        # Test that a Booking gets created
        booking = Booking.objects.get()

        self.assertEqual(customer, booking.customer)
        self.assertEqual(booking.checkin_date, inputs['checkin_date'])
        self.assertEqual(booking.checkout_date, inputs['checkout_date'])

        # Test that the verification email gets sent
        self.assertEqual(1, len(mail.outbox))

        email = mail.outbox[0]
        self.assertIn('verify email address', email.body)
```