# Flappy-Bird
import pygame
import sys
import random

# Initialisation de Pygame
pygame.init()

# Dimensions de la fenêtre
LARGEUR = 400
HAUTEUR = 600

# Couleurs
BLANC = (255, 255, 255)
NOIR = (0, 0, 0)
BLEU_CIEL = (135, 206, 250)
VERT = (0, 200, 0)

# Création de la fenêtre
fenetre = pygame.display.set_mode((LARGEUR, HAUTEUR))
pygame.display.set_caption("Flappy Bird")

# Horloge pour contrôler la vitesse
horloge = pygame.time.Clock()

# Police pour afficher le score
police = pygame.font.Font(None, 36)

# Variables du jeu
GRAVITE = 0.5
IMPULSION = -10
VITESSE_TUYAU = -4
intervalle_tuyaux = 150

# Classe pour l'oiseau
class Oiseau:
    def __init__(self):
        self.x = 50
        self.y = HAUTEUR // 2
        self.vitesse_y = 0
        self.rayon = 15

    def sauter(self):
        self.vitesse_y = IMPULSION

    def bouger(self):
        self.vitesse_y += GRAVITE
        self.y += self.vitesse_y

    def afficher(self):
        pygame.draw.circle(fenetre, NOIR, (self.x, int(self.y)), self.rayon)

# Classe pour les tuyaux
class Tuyau:
    def __init__(self, x):
        self.x = x
        self.largeur = 60
        self.hauteur_haut = random.randint(100, 400)
        self.hauteur_bas = HAUTEUR - self.hauteur_haut - intervalle_tuyaux

    def bouger(self):
        self.x += VITESSE_TUYAU

    def afficher(self):
        pygame.draw.rect(fenetre, VERT, (self.x, 0, self.largeur, self.hauteur_haut))
        pygame.draw.rect(fenetre, VERT, (self.x, HAUTEUR - self.hauteur_bas, self.largeur, self.hauteur_bas))

    def collision(self, oiseau):
        if oiseau.x + oiseau.rayon > self.x and oiseau.x - oiseau.rayon < self.x + self.largeur:
            if oiseau.y - oiseau.rayon < self.hauteur_haut or oiseau.y + oiseau.rayon > HAUTEUR - self.hauteur_bas:
                return True
        return False

# Fonction principale
def jeu():
    oiseau = Oiseau()
    tuyaux = [Tuyau(LARGEUR + 200)]
    score = 0

    running = True
    while running:
        fenetre.fill(BLEU_CIEL)

        # Gestion des événements
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    oiseau.sauter()

        # Déplacement et affichage de l'oiseau
        oiseau.bouger()
        oiseau.afficher()

        # Déplacement et gestion des tuyaux
        for tuyau in tuyaux:
            tuyau.bouger()
            tuyau.afficher()

            # Vérification de collision
            if tuyau.collision(oiseau):
                running = False

            # Vérifie si un tuyau est passé
            if tuyau.x + tuyau.largeur < 0:
                tuyaux.remove(tuyau)
                tuyaux.append(Tuyau(LARGEUR + 200))
                score += 1

        # Vérifie si l'oiseau est hors de l'écran
        if oiseau.y - oiseau.rayon < 0 or oiseau.y + oiseau.rayon > HAUTEUR:
            running = False

        # Affichage du score
        texte_score = police.render(f"Score: {score}", True, NOIR)
        fenetre.blit(texte_score, (10, 10))

        # Met à jour l'écran
        pygame.display.flip()

        # Contrôle la vitesse de la boucle
        horloge.tick(60)

    # Fin de partie
    print(f"Votre score final : {score}")
    pygame.quit()
    sys.exit()

# Lancer le jeu
if __name__ == "__main__":
    jeu()

