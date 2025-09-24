import flet as ft 
from db import main_db

def main(page: ft.Page):
    page.title = "Список покупок"
    page.theme_mode = ft.ThemeMode.LIGHT

    task_list = ft.Column(spacing=10)
    filter_type = 'all'

    def load_task():
        task_list.controls.clear()
        for task_id, task_text, completed in main_db.get_tasks(filter_type):
            task_list.controls.append(create_task_row(task_id, task_text, completed))
        page.update()

    def create_task_row(task_id, task_text, completed):
        task_field = ft.TextField(value=task_text, read_only=True, expand=True)

        task_checkbox = ft.Checkbox(
            value=bool(completed),
            on_change=lambda e: toggle_task(task_id, e.control.value)
        )

        def enable_edit(_):
            task_field.read_only = False
            task_field.update()

        enable_button = ft.IconButton(icon=ft.Icons.EDIT, on_click=enable_edit, tooltip='Редактировать')

        def save_task(_):
            main_db.update_task(task_id, task_field.value)
            task_field.read_only = True 
            task_field.update()
            page.update()

        save_button = ft.IconButton(icon=ft.Icons.SAVE_ALT_ROUNDED, tooltip='Сохранить', on_click=save_task)

        # Новая кнопка удаления
        def delete_task(_):
            main_db.delete_task(task_id)
            load_task()

        delete_button = ft.IconButton(icon=ft.Icons.DELETE_FOREVER_ROUNDED, tooltip='Удалить', on_click=delete_task)

        return ft.Row([task_checkbox, task_field, enable_button, save_button, delete_button], alignment=ft.MainAxisAlignment.START)

    def add_task(_):
        if task_input.value:
            task = task_input.value
            task_id = main_db.add_task(task)
            task_list.controls.append(create_task_row(task_id=task_id, task_text=task, completed=False))
            task_input.value = ''
            page.update()

    def toggle_task(task_id, is_completed):
        main_db.update_task(task_id=task_id, completed=int(is_completed))
        load_task()

    def set_filter(filter_value):
        nonlocal filter_type
        filter_type = filter_value
        load_task()

    task_input = ft.TextField(label="Добавить в корзину", read_only=False, expand=True, on_submit=add_task)
    add_icon_button = ft.IconButton(icon=ft.Icons.ADD_COMMENT, tooltip='Добавить задачу', on_click=add_task)
    add_text_button = ft.ElevatedButton(text="Добавить в список", on_click=add_task)

    filter_buttons = ft.Row(controls=[
        ft.ElevatedButton('Вся корзина', on_click=lambda e: set_filter('all')),
        ft.ElevatedButton('Купленное', on_click=lambda e: set_filter('completed')),
        ft.ElevatedButton("Некуплено", on_click=lambda e: set_filter('uncompleted'))
    ], alignment=ft.MainAxisAlignment.SPACE_EVENLY)

    page.add(
        ft.Column([
            ft.Row([task_input, add_icon_button], alignment=ft.MainAxisAlignment.SPACE_BETWEEN),
            ft.Row([add_text_button], alignment=ft.MainAxisAlignment.END),
            filter_buttons,
            task_list
        ])
    )

    load_task()

if __name__ == "__main__":
    main_db.init_db()
    ft.app(target=main)
