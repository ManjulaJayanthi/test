use actix_web::{web::get, App, HttpResponse, HttpServer};
use dotenv::dotenv;
use user::{get_userinfo, get_userinfo_from_file};

mod route_mock;

mod user;


async fn index() -> HttpResponse {
    HttpResponse::Ok().body("Hello")
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    dotenv().ok();

    println!("\n service running ...");

    HttpServer::new(|| {
        App::new()
            .route("/", get().to(index))
            .service(get_userinfo)
            .service(get_userinfo_from_file)
    })
    .bind(("127.0.0.1", 8080))?
    .run()
    .await
}
