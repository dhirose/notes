---
tags:
- SpringBoot
---

# フラッシュスコープ
フラッシュスコープは、HTTPリクエストをまたがってサーバ側でデータを共有するためのSpring独自の仕組み。

## 更新系処理の2重送信問題
入力⇒確認⇒完了と登録や更新の画面がある場合で、確認画面の送信ボタンを押してPOSTリクエストを送った後、再読み込み（F5等）行った場合、再度リクエストが行われてしまう。

## リダイレクトによる解決（RPGパターン）
RPGパターンは確認画面の送信ボタンでPOSTリクエストが送信されると、サーバ側がDB更新した後ブラウザ側にリダイレクトレスポンスを返す。  
ブラウザはリダイレクト先の完了画面を要求するGETリクエストを送信して完了画面を表示する。  
再読み込みが行われた場合でも、完了画面を要求するGETリクエストが送信されるだけだから、2重に更新されなくてよくなる。

RPGはPost Redirect Getの頭文字。  
更新系のPOSTのリクエストの後、リダイレクトとしてGETのリクエストが送信されることからこのような名前になっている。

## リダイレクト元と先でのデータの共有問題
例えば、リダイレクト元の注文確定処理で発行した注文IDをリダイレクト先の画面に表示したい場合などがあるとする。

そのような場合、以下のような問題が出てくる。  

- Modelオブジェクトは、リクエストごとに新しく生成されレスポンスを返すと破棄される。
- セッションスコープのデータは、明示的にクリアしないとサーバのメモリ上に残り続けてしまうから、リダイレクトの処理が終わった後に破棄したい。

## フラッシュスコープによる解決
リダイレクト元と先でのデータの共有問題を解決する仕組みがフラッシュスコープ。  
フラッシュスコープはリダイレクト元と先だけでデータを共有するための箱のようなもの。  
リダイレクト先のリクエストが終了すると、フラッシュスコープは自動的に破棄される。

## RPGパターンとフラッシュスコープの適用
=== "フラッシュスコープを使用する前のサンプル"

    ``` java title="フラッシュスコープを使用する前のサンプル"
    @Controller
    @RequestMapping("/reservation")
    public class ReservationController {
        ...
        @PostMapping(value = "/reserve", params = "reserve")
        public String reserve(Model model) {
            ReservationInput reservationInput = reservationSession.getReservationInput();
            Reservation reservation = reservationService.reserve(reservationInput);
            model.addAttribute("reservation", reservation);
            reservationSession.clearData();
            return "reservation/reservationCompletion";
        }
        ...
    }
    ```

=== "フラッシュスコープを使用したサンプル"

    ``` java title="フラッシュスコープを使用したサンプル"
    @Controller
    @RequestMapping("/reservation")
    public class ReservationController {
        ...
        @PostMapping(value = "/reserve", params = "reserve")
        public String reserve(RedirectAttributes redirectAttributes) {
            ReservationInput reservationInput = reservationSession.getReservationInput();
            Reservation reservation = reservationService.reserve(reservationInput);
            redirectAttributes.addFlashAttribute("reservation", reservation);
            reservationSession.clearData();
            return "redirect:/reservation/display-completion";
        }

        @GetMapping("/display-completion")
        public String displayCompletion() {
            return "reservation/reservationCompletion";
        }
        ...
    }
    ```


- RedirectAttributesはフラッシュスコープの箱を管理する変数。
- RedirectAttributes::addFlashAttributeにデータを渡すと、フラッシュスコープで保持される。
    - 第一引数で名前を指定する（指定しなければ、クラス名の先頭小文字の文字列が自動的に名前になる）
    - 第二引数にフラッシュスコープに格納するオブジェクトを指定する
- displayCompletionメソッドはリダイレクト先のハンドラ
    - このメソッドが呼び出されたタイミングでは、フラッシュスコープのデータがModelオブジェクトに格納された状態となっている。